This repo demonstrates the problem of "offline installations of a package with VCS URL fails even if the dependency is already installed."

1. With a connection to the internet, `pip install .` pulls the VCS URL at install time and succeeds:
```
Processing /Users/alkasm/prog/test-vcs-url
  Preparing metadata (setup.py) ... done
Collecting theta-alkasm@ git+ssh://git@github.com/alkasm/theta
  Cloning ssh://****@github.com/alkasm/theta to /private/var/folders/f0/_dkywzrd7f3bd4hdj2bmfv4m0000gp/T/pip-install-2yry1xt_/theta-alkasm_50aee5ce640c490494d6c7fabdb084fc
  Running command git clone --filter=blob:none --quiet 'ssh://****@github.com/alkasm/theta' /private/var/folders/f0/_dkywzrd7f3bd4hdj2bmfv4m0000gp/T/pip-install-2yry1xt_/theta-alkasm_50aee5ce640c490494d6c7fabdb084fc
  Resolved ssh://****@github.com/alkasm/theta to commit 796aa646d65c998cf9b68370d22f0e1a2dc0bd87
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Preparing metadata (pyproject.toml) ... done
Using legacy 'setup.py install' for test-pkg, since package 'wheel' is not installed.
Installing collected packages: test-pkg
  Running setup.py install for test-pkg ... done
Successfully installed test-pkg-0.0.0
```
2. If you then disconnect from the internet and try to `pip install .` again (tested with 22.1.2), the package will fail to install with an error like:
```
pip install .
Processing /Users/alkasm/prog/test-vcs-url
  Preparing metadata (setup.py) ... done
Collecting theta-alkasm@ git+ssh://git@github.com/alkasm/theta
  Cloning ssh://****@github.com/alkasm/theta to /private/var/folders/f0/_dkywzrd7f3bd4hdj2bmfv4m0000gp/T/pip-install-c25yoka6/theta-alkasm_fb490879532a46778a6bce77390048c9
  Running command git clone --filter=blob:none --quiet 'ssh://****@github.com/alkasm/theta' /private/var/folders/f0/_dkywzrd7f3bd4hdj2bmfv4m0000gp/T/pip-install-c25yoka6/theta-alkasm_fb490879532a46778a6bce77390048c9
  ssh: connect to host github.com port 22: Undefined error: 0
  fatal: Could not read from remote repository.

  Please make sure you have the correct access rights
  and the repository exists.
  error: subprocess-exited-with-error

  × git clone --filter=blob:none --quiet 'ssh://****@github.com/alkasm/theta' /private/var/folders/f0/_dkywzrd7f3bd4hdj2bmfv4m0000gp/T/pip-install-c25yoka6/theta-alkasm_fb490879532a46778a6bce77390048c9 did not run successfully.
  │ exit code: 128
  ╰─> See above for output.

  note: This error originates from a subprocess, and is likely not a problem with pip.
error: subprocess-exited-with-error

× git clone --filter=blob:none --quiet 'ssh://****@github.com/alkasm/theta' /private/var/folders/f0/_dkywzrd7f3bd4hdj2bmfv4m0000gp/T/pip-install-c25yoka6/theta-alkasm_fb490879532a46778a6bce77390048c9 did not run successfully.
│ exit code: 128
╰─> See above for output.

note: This error originates from a subprocess, and is likely not a problem with pip.
```
3. If you instead opt for the legacy resolver via `pip install . --use-deprecated legacy-resolver` it will succeed (assuming the dependency has been installed previously):
```
Processing /Users/alkasm/prog/test-vcs-url
  Preparing metadata (setup.py) ... done
Requirement already satisfied: theta-alkasm@ git+ssh://git@github.com/alkasm/theta from git+ssh://****@github.com/alkasm/theta in ./venv/lib/python3.8/site-packages (from test-pkg==0.0.0) (1.2.0)
Using legacy 'setup.py install' for test-pkg, since package 'wheel' is not installed.
Installing collected packages: test-pkg
  Attempting uninstall: test-pkg
    Found existing installation: test-pkg 0.0.0
    Uninstalling test-pkg-0.0.0:
      Successfully uninstalled test-pkg-0.0.0
  Running setup.py install for test-pkg ... done
Successfully installed test-pkg-0.0.0
```
