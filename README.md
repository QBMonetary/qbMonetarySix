[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/ambv/black)

README for qbMonetary
=============================

Integration tests for qbMonetary

Installation
------------

```sh
# create and activate virtual env
$ python3 -m venv .env
$ . .env/bin/activate
# install this package together with dev requirements
$ make install
```

Requires Python 3.8 or newer.

Usage
-----

Preparing env:

```sh
# cd to qbMonetary repo
$ cd /path/to/qbMonetary
# update and checkout the desired commit/tag
$ git checkout master
$ git pull origin master
$ git fetch --all --tags
$ git checkout tags/<tag>
# launch devops shell
$ nix-shell -A devops
# cd to tests repo
$ cd /path/to/qbMonetary
# activate virtual env
$ . .env/bin/activate
```

Running tests:

```sh
# run tests
$ make tests
```

Running tests on one of the testnets:

```sh
# run tests
$ BOOTSTRAP_DIR=/path/to/bootstrap/dir make testnets
```

Running individual tests:

```sh
$ pytest -k "test_name1 or test_name2" cardano_node_tests
```

Running linter:

```sh
# activate virtual env
$ . .env/bin/activate
# run linter
$ make lint
```

Variables for `make tests` and `make testnets`
----------------------------------------------

* `SCHEDULING_LOG` - specifies path to file where log messages for tests and cluster instance scheduler are stored
* `PYTEST_ARGS` - specifies additional arguments for pytest
* `TEST_THREADS` - specifies number of pytest workers
* `CLUSTERS_COUNT` - number of cluster instances that will be started
* `CLUSTER_ERA` - cluster era for cardano node - used for selecting correct cluster start script
* `TX_ERA` - era for transactions - can be used for creating Shelley-era (Allegra-era, ...) transactions
* `NOPOOLS` - when running tests on testnet, a cluster with no staking pools will be created
* `BOOTSTRAP_DIR` - path to a bootstrap dir for given testnet (genesis files, config files, faucet data)

E.g.
```sh
$ SCHEDULING_LOG=testrun_20201208_1.log TEST_THREADS=3 CLUSTER_ERA=mary TX_ERA=shelley PYTEST_ARGS="-k 'test_stake_pool_low_cost or test_reward_amount'" make tests
```


Tests development
-----------------

When running tests, the framework start and stop cluster instances as needed. That is not ideal for tests development, as starting a cluster instance takes several epochs (to get from Byron to Mary). To keep cardano cluster running in-between test runs, one needs to start it in "development mode".

```sh
# activate virtual env
$ . .env/bin/activate
# prepare cluster scripts
$ prepare-cluster-scripts -d scripts/destination/dir -s cardano_node_tests/cluster_scripts/mary/
# set env variables
$ export CARDANO_NODE_SOCKET_PATH=/path/to/cardano-node/state-cluster0/bft1.socket DEV_CLUSTER_RUNNING=1
# start the cluster instance in development mode
$ scripts/destination/dir/start-cluster-hfc
```


Test coverage of cardano-cli commands
-------------------------------------

To get test coverage of cardano-cli commands, run tests as usual (`make tests`) and generate the coverage report JSON file with

```
$ cardano-cli-coverage -i .cli_coverage/cli_coverage_*.json -o .cli_coverage/coverage_report.json
```


Publishing testing results
--------------------------

Clone https://github.com/mkoura/cardano-node-tests-reports and see its [README](https://github.com/mkoura/cardano-node-tests-reports/blob/main/README.md).


Building documentation
----------------------

To build documentation using Sphinx, run

```
$ make doc
```

The documentation is generated to `docs/build/html`.

To publish documentation to https://input-output-hk.github.io/cardano-node-tests/, run:

```sh
# checkout the "github_pages" branch
$ git checkout github_pages
# copy/move content of docs/build/html to docs
$ mv docs/build/html/* docs/
# stage changes
$ git add docs
# commit changes
$ git commit
# push to origin/github_pages (upstream/github_pages)
$ git push origin github_pages
```



Contributing
------------

Install this package and its dependencies as described above.

Run `pre-commit install` to set up the git hook scripts that will check you changes before every commit. Alternatively run `make lint` manually before pushing your changes.

Follow the [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html), with the exception that formatting is handled automatically by [Black](https://github.com/psf/black) (through `pre-commit` command).
