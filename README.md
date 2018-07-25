This repository contains the documentation for the Insekta platform components:

* insekta-web
* insekta-vm
* openvpn, i.e., the configuration

You can find the documentation online at [Read the Docs](https://insekta.readthedocs.io/en/latest/).

Invoke the following commands in a local clone of this repository to build the documentation:

```
$ pipenv install
$ pipenv run make html
```

The generated HTML files can then be found in `_build/html`.
