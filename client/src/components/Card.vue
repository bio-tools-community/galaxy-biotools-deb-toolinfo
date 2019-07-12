<template>
    <div class="card" :class="{'checked': checked}">
        <div class="card-header">
            <input v-model="checked" type="checkbox"/>
            <span :class="{'checked': checked}">{{ path }}</span>
            <template v-if="!tool.length">
                <div class="badge badge-danger">No matches found</div>
            </template>
            <template v-else>
                <div v-if="biotoolsData.length" class="badge badge-warning">{{ biotoolsData.length }} bio.tools
                    matches
                </div>
                <div v-if="debianData.length" class="badge badge-success">{{ debianData.length }} DebMed matches</div>
            </template>

            <button class="collapser btn btn-secondary btn-sm"
                    @click="collapsed = !collapsed">
                {{ collapsed ? "⬇ uncollapse" : "⬆ collapse" }}
            </button>
        </div>

        <template v-if="!collapsed">
            <div class="alert alert-light">
                <a :href="'https://bio.tools/t?page=1&q=' + path.split('/')[path.split('/').length - 2] + '&sort=score'"
                   target="_blank">search {{ path.split('/')[path.split('/').length - 2] }} at bio.tools ↗</a> |
                <a :href="'https://salsa.debian.org/search?search=' + path.split('/')[path.split('/').length - 2]"
                   target="_blank">search {{ path.split('/')[path.split('/').length - 2] }} at Debian Med repos ↗</a>
            </div>

            <div class="card-body" v-for="(item, i) in biotoolsData">
                <h5 class="card-title">bio.tools match #{{ i + 1 }} (<span class="badge badge-info">{{ item.biotools_doi.type || "None" }}</span>
                    from <a :href="'https://doi.org/' + item.biotools_doi.doi" target="_blank">{{
                        item.biotools_doi.source }}
                        ↗</a>)</h5>

                <div v-if="path.split('/').pop().endsWith('macros.xml')" class="alert alert-danger"
                     style="margin: 20px 0;">
                    Publication extracted from macros.xml
                </div>

                <template v-if="!merge">
                    <p class="card-text">
                    <pre :style="{height: (24 + 64) + 'px'}"
                        contenteditable="true" class="code form-control">{{ indentation }}&lt;xrefs&gt;
        {{ indentation }}&lt;xref type="bio.tools"&gt;{{ item.biotools_id }}&lt;/xref&gt;
    {{ indentation }}&lt;/xrefs&gt;</pre>
                    <button class="copy btn-sm btn btn-link" @click="copy">⎘ copy</button>

                    <template v-if="item.biotools_topics.length">
                  <pre class="code form-control" :style="{height: (24 * item.biotools_topics.length + 64) + 'px'}"
                       contenteditable="true"
                       v-text="indentation + '<edam_topics>\n' + item.biotools_topics.map((topic) => {
                    return indentation + '    <edam_topic>' + topic + '</edam_topic>';
                  }).join('\n') + '\n' + indentation + '</edam_topics>'">
                  </pre>
                        <button class="copy btn-sm btn btn-link" @click="copy">⎘ copy</button>
                    </template>

                    <template v-if="item.biotools_operations.length">
                        <template v-for="operations in item.biotools_operations">
                    <pre class="code form-control" :style="{height: (24 * operations.length + 64) + 'px'}"
                         contenteditable="true"
                         v-text="indentation + '<edam_operations>\n' + operations.map((operation) => {
                      return indentation + '    <edam_operation>' + operation + '</edam_operation>';
                    }).join('\n') + '\n' + indentation + '</edam_operations>'">
                    </pre>
                            <button class="copy btn-sm btn btn-link" @click="copy">⎘ copy</button>
                        </template>
                    </template>
                    </p>
                </template>
                <template v-else>
                    <pre :style="{height: (24 * (5 + item.biotools_topics.length +
                                  item.biotools_operations.reduce((sum, operations) => {
                                    return sum + operations.length;
                                  }, 5))) + 'px'}"
                         contenteditable="true" class="code form-control" v-text="[indentation + '<xrefs>',
                         indentation + '    <xref type=&quot;bio.tools&quot;>' + item.biotools_id  + '</xref>',
                         indentation + '</xrefs>',
                         '',
                         item.biotools_topics.length ? [indentation + '<edam_topics>',
                             item.biotools_topics.map((topic) => {
                                return indentation + '    <edam_topic>' + topic + '</edam_topic>';
                             }).join('\n'),
                             indentation + '</edam_topics>'].join('\n') : '',
                         '',
                         item.biotools_operations.map((operations) => {
                             return operations.length ? [
                                    indentation + '<edam_operations>',
                                    operations.map((operation) => {
                                        return indentation + '    <edam_operation>' + operation + '</edam_operation>';
                                    }).join('\n'),
                                    indentation + '</edam_operations>',
                                    ''
                                ].join('\n') : ''
                         }).join('\n'),
                         ].join('\n')"></pre>
                    <button class="copy btn-sm btn btn-link" @click="copy">⎘ copy</button>
                </template>
            </div>

            <div class="card-body" v-for="(item, i) in debianData">
                <h5 class="card-title">Debian Med match #{{ i + 1 }}</h5>

                <p class="card-text">
                    <strong>bio.tools name:</strong> {{ item.deb_biotools_id }} <br/>

                    <template v-if="item.deb_topics.length">
                        <h6>Topics:</h6>
                        <ul>
                            <li v-for="topic in item.deb_topics"><a target="_blank"
                                                                    :href="topic.url || 'http://bioportal.bioontology.org/search?q='  + topic.value + '&ontologies=EDAM-BIOIMAGING%2CEDAM'">{{
                                topic.value }} ({{ topic.url.split('/').pop() }}) ↗</a></li>
                        </ul>
                    </template>

                    <template v-if="item.deb_operations.length">
                        <h6>Operations:</h6>
                        <ul v-for="operations in item.deb_operations">
                            <li v-for="operation in operations"><a target="_blank"
                                                                   :href="operation.url || 'http://bioportal.bioontology.org/search?q='  + operation.value + '&ontologies=EDAM-BIOIMAGING%2CEDAM'">{{
                                operation.value }} ({{ operation.url.split('/').pop() }}) ↗</a></li>
                        </ul>
                    </template>
                </p>

            </div>
        </template>

        <textarea ref="copy"></textarea>
    </div>
</template>

<script>
    import _ from 'lodash';

    export default {
        name: "Card",
        props: ['tool', 'path', 'merge', 'indent'],
        data() {
            return {
                'checked': false,
                'collapsed': false,
            }
        },
        computed: {
            indentation() {
                let indent = "";
                for (let i = 0; i < this.indent; i++) {
                    indent += " "
                }
                return indent;
            },
            biotoolsData() {
                return _.filter(this.tool, (data) => {
                    return data.type === 'bio.tools';
                });
            },
            debianData() {
                return _.filter(this.tool, (data) => {
                    return data.type === 'debmed';
                });
            }
        },
        watch: {
            checked(newVal) {
                if (newVal) {
                    this.collapsed = true;
                } else {
                    this.collapsed = false;
                }
            }
        },
        methods: {
            copy(e) {
                this.$refs['copy'].value = e.target.previousElementSibling.innerText;

                e.target.innerText = '✓ copied';

                this.$refs['copy'].select();

                document.execCommand('selectAll', false, null);
                document.execCommand('copy');

                let selection = window.getSelection();
                let range = document.createRange();
                range.selectNodeContents(e.target.previousElementSibling);
                selection.removeAllRanges();
                selection.addRange(range);

                setTimeout(() => {
                    e.target.innerText = '⎘ copy';
                    this.$refs['copy'].value = '';
                }, 1000);
            },
        }
    }
</script>

<style scoped>

    textarea {
        position: absolute;
        top: -1000px;
        left: -1000px;
    }

    .card {
        margin: 10px;
    }

    .card-header {
        font-family: SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
    }

    .code {
        font-family: SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
    }

    .card.checked {
        background-color: rgba(100, 149, 237, 0.2);
    }

    span.checked {
        text-decoration: line-through;
    }

    .collapser {
        float: right;
    }

    .copy {
        position: relative;
        top: -15px;
    }

</style>
