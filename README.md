# Group Web Service tester
These are some Json representation tests we can run when deploying/migrating.

## Settings and groups setup
* Copy settings.tmpl to settings.py and edit that for your local configuration.
* You must create the base group, as identified in your settings.py, and permit your certificate to be a subgroup creator.  Call it "u\_\<yournetid>\_test".
* You must identify your client and ca certs.
* The membership tests use real ids.

## Python setup
### As root:
```
    # yum install openssl11 openssl11-devel -y
(This appears to get wiped every Tuesday night, but is needed to build a modern Python)
    # cd /data/local/src/
    # wget https://www.python.org/ftp/python/3.14.5/Python-3.14.5.tgz
    # gunzip Python-3.14.5.tgz
    # tar xvf Python-3.14.5.tar
    # cd /data/local/src/Python-3.14.5/
    # ./configure --prefix=/data/local/python3
(which sets the 'altinstall' installation directory. I tried './configure --enable-optimizations --prefix=/data/local/python3', but that resulted in
    make[1]: *** [Python/frozen_modules/abc.h] Segmentation fault
 when running the next command)
    # make -j1
(# make -j$(nproc) also results in a segmentation fault :( )
    # make altinstall
```
This installs the desired python in
    /data/local/python3
### Then, as iamgws in /data/local/src/gws-svc-tests
```
    $ /data/local/python3/bin/python3.14 -m venv .venv
    $ . .venv/bin/activate
```
and, to check your environment is set correctly
```
    $ python --version
    Python 3.14.5
```

## Testing

* Make a virtual env and install what's in requirements.txt. The latest version of python I've been able to run these tests with is 3.8.14, as some newer version introduced some breaking changes.<br>

`$ pyenv local 3.8.14`
`$ virtualenv -p 3.8.14 env`
`$ . ./env/bin/activate`
`$ pip install -r requirements.txt`
* You may be prompted to run (which you'll probably want to)
`$ pip install --upgrade pip`

`$ nosetests .`
... or
`$ nosetests --with-timer .`
... or
`$ pynose`
to get timing information for each test run
* The tests should clean up after themselves.

Note that some of the tests rely on ldap membership synchronizing.  That requires the ldap_provisioner running on the target service.

## Style

* Use: pycodestyle --max-line-length=150  *.py test
