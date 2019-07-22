<template>
    <div>
        <h1>Report <span :style="{
      'cursor': 'pointer',
      'float': 'right',
      'filter': mode === '🌞' ? 'invert(0)' : 'invert(1)',
    }" @click="changeMode" :title="mode === '🌞' ? 'light mode' : 'dark mode'">{{ mode }}</span></h1>


        <div class="header card">
            <div class="card-header">
                <strong>Statistics</strong>
            </div>
            <div class="card-body">
                # of tools: {{ numberOfTools }}
                <br/>
                # of annotations: {{ numberOfAnnotations }}
                <hr/>
                # of tools with at least one <strong>bio.tools</strong> match: {{ atLeastOneBioTools }}
                <br/>
                # of tools with at least one <strong>Debian Med</strong> match: {{ atLeastOneDebian }}
                <br/>
                # of tools with at least one match with <strong>any</strong> data source <em>(or)</em>:
                {{ atLeastOneOr }}
                <br/>
                # of tools with at least one match with <strong>both</strong> data sources <em>(and)</em>:
                {{ atLeastOneAnd }}
                <hr/>
                # of <strong>bio.tools</strong> annotations: {{ bioToolsAnnotations }}
                <br/>
                # of <strong>Debian Med</strong> annotations: {{ debianMedAnnotations }}
            </div>
        </div>

        <div class="header card">
            <div class="card-header">
                <strong>Settings</strong>
            </div>
            <div class="card-body">
                <label><input type="checkbox"  v-model="merge" /> Merge textareas</label> <span class="text-muted">|</span>
                <label><input type="number" min="0" max="32" v-model="indent" /> indent</label>
            </div>
        </div>

        <card v-for="(galaxyToolData, path) in sorted" :key="path" :tool="galaxyToolData" :path="path" :merge="merge" :indent="indent"></card>

    </div>
</template>

<script>
    import report from '../tool_annotations.json';

    import Card from './Card';

    import _ from 'lodash';

    export default {
        name: 'HelloWorld',
        components: {Card},
        data() {
            return {
                report,
                mode: '🌞',
                merge: false,
                indent: 4
            }
        },
        methods: {
            changeMode() {
                if (this.mode === "🌞") {
                    document.querySelector('html').style.filter = 'invert(1)';
                    this.mode = "🌚";
                } else {
                    document.querySelector('html').style.filter = 'invert(0)';
                    this.mode = "🌞";
                }
            }
        },
        computed: {
            numberOfTools() {
                return Object.keys(this.report).length;
            },
            numberOfAnnotations() {
                return _.reduce(this.sorted, (sum, items) => {
                    return sum + _.reduce(items, (sum, item) => {
                        return sum + 1
                    }, 0);
                }, 0);
            },
            bioToolsAnnotations() {
                return _.reduce(this.sorted, (sum, items) => {
                    return sum + _.reduce(items, (sum, item) => {
                        return item.type === 'bio.tools' ? sum + 1 : sum
                    }, 0);
                }, 0);
            },
            debianMedAnnotations() {
                return _.reduce(this.sorted, (sum, items) => {
                    return sum + _.reduce(items, (sum, item) => {
                        return item.type === 'debmed' ? sum + 1 : sum
                    }, 0);
                }, 0);
            },
            atLeastOneBioTools() {
                return _.filter(this.sorted, (items) => {
                    return _.filter(items, (item) => {
                        return item.type === 'bio.tools'
                    }).length > 0;
                }).length;
            },
            atLeastOneDebian() {
                return _.filter(this.sorted, (items) => {
                    return _.filter(items, (item) => {
                        return item.type === 'debmed'
                    }).length > 0;
                }).length;
            },
            atLeastOneOr() {
                return _.filter(this.sorted, (items) => {
                    return _.filter(items, (item) => {
                            return item.type === 'debmed'
                        }).length > 0
                        || _.filter(items, (item) => {
                            return item.type === 'bio.tools'
                        }).length > 0;
                }).length;
            },
            atLeastOneAnd() {
                return _.filter(this.sorted, (items) => {
                    return _.filter(items, (item) => {
                            return item.type === 'debmed'
                        }).length > 0
                        && _.filter(items, (item) => {
                            return item.type === 'bio.tools'
                        }).length > 0;
                }).length;
            },
            sorted() {
                let ordered = {};
                Object.keys(this.report).sort().forEach(key => ordered[key] = this.report[key]);
                return ordered;
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

    .header {
        background-color: #eee;
        margin: 10px;
    }

    h1 {
        text-align: center;
    }

    h1, h2 {
        font-weight: normal;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

</style>
