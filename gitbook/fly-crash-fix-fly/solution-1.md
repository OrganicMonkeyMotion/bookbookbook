---
description: Try the MSCODE install route for the ESP-IDF, see if that solves the problem.
---

# Solution 1

So, I deleted all instances of the esp-idf, and any other esp-idf offering, to start from scratch with vscode. &#x20;

To wit, it tweren't me.  Very happy I get the same error when using the vscode esp-idf wizard.  Note also, the tick box "Show all ESP-IDF tags".  That was necessary as the "Select ESP-IDF version:" drop down was limited to V5.x otherwise.

<figure><img src="../.gitbook/assets/esp-idf 4.0 broken.png" alt=""><figcaption><p>It weren't me!</p></figcaption></figure>

But of course, the problem with the venv breakage also manifests when installing esp-idf v4.0 this way.

<pre><code>non zero exit code 1

Collecting virtualenv
  Downloading virtualenv-20.28.0-py3-none-any.whl (4.3 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 4.3/4.3 MB 6.1 MB/s eta 0:00:00
Collecting distlib&#x3C;1,>=0.3.7
  Downloading distlib-0.3.9-py2.py3-none-any.whl (468 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 469.0/469.0 KB 5.8 MB/s eta 0:00:00
Collecting filelock&#x3C;4,>=3.12.2
  Downloading filelock-3.16.1-py3-none-any.whl (16 kB)
Collecting platformdirs&#x3C;5,>=3.9.1
  Downloading platformdirs-4.3.6-py3-none-any.whl (18 kB)
Installing collected packages: distlib, platformdirs, filelock, virtualenv
  WARNING: The script virtualenv is installed in '/home/asterion/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
Successfully installed distlib-0.3.9 filelock-3.16.1 platformdirs-4.3.6 virtualenv-20.28.0
usage: virtualenv [--version] [--with-traceback] [-v | -q] [--read-only-app-data] [--app-data APP_DATA] [--reset-app-data] [--upgrade-embed-wheels] [--discovery {builtin}] [-p py] [--try-first-with py_exe]
                  [--creator {builtin,cpython3-posix,venv}] [--seeder {app-data,pip}] [--no-seed] [--activators comma_sep_list] [--clear] [--no-vcs-ignore] [--system-site-packages] [--symlinks | --copies] [--no-download | --download]
                  [--extra-search-dir d [d ...]] [--pip version] [--setuptools version] [--wheel version] [--no-pip] [--no-setuptools] [--no-wheel] [--no-periodic-update] [--symlink-app-data] [--prompt prompt] [-h]
                  dest
<strong>virtualenv: error: unrecognized arguments: --no-site-packages
</strong>SystemExit: 2
Creating a new Python environment in /home/asterion/.espressif/python_env/idf4.0_py3.10_env
Installing virtualenv
Traceback (most recent call last):
  File "/home/asterion/esp/v4.0/esp-idf/tools/idf_tools.py", line 1372, in &#x3C;module>
    main(sys.argv[1:])
  File "/home/asterion/esp/v4.0/esp-idf/tools/idf_tools.py", line 1368, in main
    action_func(args)
  File "/home/asterion/esp/v4.0/esp-idf/tools/idf_tools.py", line 1183, in action_install_python_env
    subprocess.check_call([sys.executable, '-m', 'virtualenv', '--no-site-packages', idf_python_env_path],
  File "/usr/lib/python3.10/subprocess.py", line 369, in check_call
    raise CalledProcessError(retcode, cmd)
subprocess.CalledProcessError: Command '['/usr/bin/python3', '-m', 'virtualenv', '--no-site-packages', '/home/asterion/.espressif/python_env/idf4.0_py3.10_env']' returned non-zero exit status 2.

</code></pre>



```
virtualenv: error: unrecognized arguments: --no-site-packages

https://github.com/pypa/virtualenv/issues/1681

--no-site-packages flag dropped
```

Turns out the error is because virtualenv dropped the flag "--no-site-packages flag.

Fix (really a hack) is the fail is second call of two to /tools/idf\_tools.py in install.sh, so I hacked up a file install2nHalf.sh below. Ran it from the shell inside of vscode.

```
#!/usr/bin/env bash

set -e
set -u

export IDF_PATH=$(cd $(dirname $0); pwd)

echo "Installing Python environment and packages"
${IDF_PATH}/tools/idf_tools.py install-python-env

basedir="$(dirname $0)"
echo "All done! You can now run:"
echo ""
echo "  . ${basedir}/export.sh"
echo ""
```

This actually means I can go back (maybe) and try the hand build - one day.

The trick is in /tools/idf\_tools.py there is one instance of "--no-site-package" inside the function:

```
def action_install_python_env(args):
   // blah blah
   ...
   // was
   subprocess.check_call([sys.executable, '-m', 'virtualenv', '--no-site-package',idf_python_env_path],
      stdout=sys.stdout, stderr=sys.stderr)
   // is now
   subprocess.check_call([sys.executable, '-m', 'virtualenv', idf_python_env_path],
      stdout=sys.stdout, stderr=sys.stderr)
```

Delete that sucker, taking care with the function signature, then run:

```
./install2ndHalf.sh
```



