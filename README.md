# galaxy-biotools-deb-toolinfo

**Integration of tools information in Galaxy IUC Tools (primary) with Bio.tools and Debian Med**



## Authors

- Ivan Kuzmin [@inkuzmin](https://twitter.com/inkuzmin)
- Matúš Kalaš [@matuskalas](https://twitter.com/matuskalas)

## Contributors

- Hervé Ménager [@rvmngr](https://twitter.com/rvmngr)
- Galaxy and especially Galaxy IUC contributors (numerous, including Björn Grüning [@bjoerngruening](https://twitter.com/bjoerngruening), Nicola Soranzo [@NicolaSoranzo](https://twitter.com/NicolaSoranzo), *etc.*)
- Bio.tools curators and contributors (numerous, including Hans Ienasescu [@HansIenasescu](https://twitter.com/HansIenasescu) and Jon Ison)
- Debian Med contributors (of those especially Steffen Möller, Andreas Tille, [@ssatta](https://twitter.com/ssatta), Michael R. Crusoe [@biocrusoe](https://twitter.com/biocrusoe), *etc.*)
- `get_doi` function is based on the implementation of DOI fetcher by Kenzo-Hugo Hillion [@khhillion](https://twitter.com/khhillion)


## Based on previous work

- Adding tools annotation from Bio.tools into Galaxy IUC Tools by @inkuzmin (PR [#1905](https://github.com/galaxyproject/tools-iuc/pull/1905) and [#1897](https://github.com/galaxyproject/tools-iuc/pull/1897) in *galaxyproject/tools-iuc*)
- Retrieving tools annotation from Debian by @tillea ([*edam.sh* in *bio-tools/biotoolsConnect*](https://github.com/bio-tools/biotoolsConnect/blob/master/DebianMed/edam.sh))
- Succession of integration efforts of tools information in Debian Med and Bio.tools by @smoe, @rvmngr, @matuskalas ([*bio-tools-community/debian-med-links-analysis*](https://github.com/bio-tools-community/debian-med-links-analysis), [*bio-tools-community/json-buffer*](https://github.com/bio-tools-community/json-buffer), [*bio-tools-community/debian2biotools*](https://github.com/bio-tools-community/debian2biotools), archived [*toolinfowarehouse*](https://github.com/bio-tools-community/toolinfowarehouse))



## Licenses

Choice of MIT and [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/)


## Interactive report creation

Interactive report creation code consists of two parts:
1. A Jupyter notebook to find matches by DOI and generate JSON file with the data;
2. A client to display data in a browser in a form that facilitates the process of manual annotation of the Galaxy Tools with EDAM terms.

## To start, explore, and rerun the notebook it is advisable to create a python3 virtual environment:

```bash
# create a virtual environment
$ python3 -m venv .venv

# activate the virtual environment
$ . .venv/bin/activate

# install Jupyter and start the notebooks server
$ pip install jupyter
$ jupyter notebook

# open the notebook in a browser: http://localhost:8888/notebooks/workflow.ipynb

# after all work is done to end the session:
(.venv)$ deactivate
```

## To build the client:

``` bash
# naviagate to the `client` folder with
$ cd client

# install dependencies
$ npm install

# serve with hot reload at localhost:8080
$ npm run dev

# build for production with minification and serve it with HTTP server
$ npm run build
$ cd dist
$ python -m SimpleHTTPServer 8000
```


## TODO
- [] Annotate Galaxy instances (https://github.com/galaxyproject/galaxy-hub/tree/master/src/use)