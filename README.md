# 实验log - 记安装运行stable diffusion models

# 更新时间
2023.8.29

## 问题:
1. 安装stable-diffusion遇到安装包错误；
```shell
ModuleNotFoundError: No module named 'torch'
```

而实际torch已安装(不确定安装在哪个位置) - 通过pip list | grep torch 获得结果
```shell
pytorch-lightning        1.9.0
torch                    2.0.0+cu118
torchaudio               2.0.0
torchmetrics             1.1.0
torchvision              0.15.1+cu118
```

完整报错内容:
```shell
Getting requirements to build wheel ... error
  error: subprocess-exited-with-error
  
  × Getting requirements to build wheel did not run successfully.
  │ exit code: 1
  ╰─> [17 lines of output]
      Traceback (most recent call last):
        File "/home/flyslice/invokeai/.venv/lib/python3.9/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 353, in <module>
          main()
        File "/home/flyslice/invokeai/.venv/lib/python3.9/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 335, in main
          json_out['return_val'] = hook(**hook_input['kwargs'])
        File "/home/flyslice/invokeai/.venv/lib/python3.9/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 118, in get_requires_for_build_wheel
          return hook(config_settings)
        File "/tmp/pip-build-env-4qyukfuv/overlay/lib/python3.9/site-packages/setuptools/build_meta.py", line 355, in get_requires_for_build_wheel
          return self._get_build_requires(config_settings, requirements=['wheel'])
        File "/tmp/pip-build-env-4qyukfuv/overlay/lib/python3.9/site-packages/setuptools/build_meta.py", line 325, in _get_build_requires
          self.run_setup()
        File "/tmp/pip-build-env-4qyukfuv/overlay/lib/python3.9/site-packages/setuptools/build_meta.py", line 507, in run_setup
          super(_BuildMetaLegacyBackend, self).run_setup(setup_script=setup_script)
        File "/tmp/pip-build-env-4qyukfuv/overlay/lib/python3.9/site-packages/setuptools/build_meta.py", line 341, in run_setup
          exec(code, locals())
        File "<string>", line 8, in <module>
      ModuleNotFoundError: No module named 'torch'
      [end of output]
  
  note: This error originates from a subprocess, and is likely not a problem with pip.
error: subprocess-exited-with-error

× Getting requirements to build wheel did not run successfully.
│ exit code: 1
╰─> See above for output.

note: This error originates from a subprocess, and is likely not a problem with pip.
Traceback (most recent call last):
  File "/home/flyslice/xy/diffusion/stable-demo2/InvokeAI-Installer/./lib/main.py", line 51, in <module>
    inst.install(**args.__dict__)
  File "/home/flyslice/xy/diffusion/stable-demo2/InvokeAI-Installer/lib/installer.py", line 181, in install
    self.instance.install(
  File "/home/flyslice/xy/diffusion/stable-demo2/InvokeAI-Installer/lib/installer.py", line 237, in install
    self.install_app(extra_index_url, optional_modules, find_links)
  File "/home/flyslice/xy/diffusion/stable-demo2/InvokeAI-Installer/lib/installer.py", line 316, in install_app
    pip[
  File "/tmp/invokeai-installer-tmph0whiah0/lib/python3.9/site-packages/plumbum/commands/modifiers.py", line 145, in __rand__
    cmd(
  File "/tmp/invokeai-installer-tmph0whiah0/lib/python3.9/site-packages/plumbum/commands/base.py", line 113, in __call__
    return self.run(args, **kwargs)[1]
  File "/tmp/invokeai-installer-tmph0whiah0/lib/python3.9/site-packages/plumbum/commands/base.py", line 252, in run
    return p.run()
  File "/tmp/invokeai-installer-tmph0whiah0/lib/python3.9/site-packages/plumbum/commands/base.py", line 215, in runner
    return run_proc(p, retcode, timeout)
  File "/tmp/invokeai-installer-tmph0whiah0/lib/python3.9/site-packages/plumbum/commands/processes.py", line 299, in run_proc
    return _check_process(proc, retcode, timeout, stdout, stderr)
  File "/tmp/invokeai-installer-tmph0whiah0/lib/python3.9/site-packages/plumbum/commands/processes.py", line 17, in _check_process
    proc.verify(retcode, timeout, stdout, stderr)
  File "/tmp/invokeai-installer-tmph0whiah0/lib/python3.9/site-packages/plumbum/machines/base.py", line 27, in verify
    raise ProcessExecutionError(
plumbum.commands.processes.ProcessExecutionError: Unexpected exit code: 1
Command line: | /home/flyslice/invokeai/.venv/bin/pip install --require-virtualenv --use-pep517 '/home/flyslice/xy/diffusion/stable-demo2/InvokeAI-Installer/lib/InvokeAI-3.0.2.post1-py3-none-any.whl[xformers,onnx-cuda]' --extra-index-url https://download.pytorch.org/whl/cu118
```

在安装wheel时报错，显示`/home/flyslice/invokeai/.venv/bin/pip install --require-virtualenv --use-pep517 '/home/flyslice/xy/diffusion/stable-demo2/InvokeAI-Installer/lib/InvokeAI-3.0.2.post1-py3-none-any.whl[xformers,onnx-cuda]' --extra-index-url https://download.pytorch.org/whl/cu118`运行./install.sh中的此行shell命令时报错，重新单独运行这一行还是会报相同的错误，目前还未解决。

### 安装教程

invoke-ai GitHub官网

[https://github.com/invoke-ai/InvokeAI/tree/main/installer](https://github.com/invoke-ai/InvokeAI/tree/main/installer)

Project homepage: https://github.com/invoke-ai/InvokeAI


### 查看install.sh:
```sh
(py39) flyslice@PR2012AM3:~/xy/diffusion/stable-demo2/InvokeAI-Installer$ cat ./install.sh 
#!/bin/bash

# make sure we are not already in a venv
# (don't need to check status)
deactivate >/dev/null 2>&1
scriptdir=$(dirname "$0")
cd $scriptdir

function version { echo "$@" | awk -F. '{ printf("%d%03d%03d%03d\n", $1,$2,$3,$4); }'; }

MINIMUM_PYTHON_VERSION=3.9.0
MAXIMUM_PYTHON_VERSION=3.11.100
PYTHON=""
for candidate in python3.11 python3.10 python3.9 python3 python ; do
    if ppath=`which $candidate`; then
        # when using `pyenv`, the executable for an inactive Python version will exist but will not be operational
        # we check that this found executable can actually run
        if [ $($candidate --version &>/dev/null; echo ${PIPESTATUS}) -gt 0 ]; then continue; fi

        python_version=$($ppath -V | awk '{ print $2 }')
        if [ $(version $python_version) -ge $(version "$MINIMUM_PYTHON_VERSION") ]; then
            if [ $(version $python_version) -le $(version "$MAXIMUM_PYTHON_VERSION") ]; then
                PYTHON=$ppath
                break
            fi
        fi
    fi
done

if [ -z "$PYTHON" ]; then
    echo "A suitable Python interpreter could not be found"
    echo "Please install Python $MINIMUM_PYTHON_VERSION or higher (maximum $MAXIMUM_PYTHON_VERSION) before running this script. See instructions at $INSTRUCTIONS for help."
    echo "For the best user experience we suggest enlarging or maximizing this window now."
    read -p "Press any key to exit"
    exit -1
fi

exec $PYTHON ./lib/main.py ${@}
read -p "Press any key to exit"
```

### 类似问题但不符合:
github上：

no model named 'torch' #740 - [https://github.com/facebookresearch/xformers/issues/740](https://github.com/facebookresearch/xformers/issues/740)

× '''Getting requirements to build wheel did not run successfully. │ exit code: 1 ╰─> [21 lines of output]''' #4595 - [https://github.com/Significant-Gravitas/Auto-GPT/issues/4595](https://github.com/Significant-Gravitas/Auto-GPT/issues/4595)

Error in setup.py "No module named 'torch'" when installing with Poetry #156 - [https://github.com/rusty1s/pytorch_sparse/issues/156](https://github.com/rusty1s/pytorch_sparse/issues/156)

### 中途尝试通过pip install InvokeAI 直接安装 仍然失败
参考[https://invoke-ai.github.io/InvokeAI/installation/010_INSTALL_AUTOMATED/#stable-diffusion-xl-generation-fails-after-trying-to-load-unet](https://invoke-ai.github.io/InvokeAI/installation/010_INSTALL_AUTOMATED/#stable-diffusion-xl-generation-fails-after-trying-to-load-unet)

#### 报错
```shell
Preparing metadata (setup.py) ... error
  error: subprocess-exited-with-error
  
  × python setup.py egg_info did not run successfully.
  │ exit code: 1
  ╰─> [17 lines of output]
      Traceback (most recent call last):
        File "/tmp/pip-install-c2b4h0c7/realesrgan_fed472ef215948b384f45de0bf3525fb/setup.py", line 48, in get_hash
          from facexlib.version import __version__
      ModuleNotFoundError: No module named 'facexlib'
      
      During handling of the above exception, another exception occurred:
      
      Traceback (most recent call last):
        File "<string>", line 2, in <module>
        File "<pip-setuptools-caller>", line 34, in <module>
        File "/tmp/pip-install-c2b4h0c7/realesrgan_fed472ef215948b384f45de0bf3525fb/setup.py", line 89, in <module>
          write_version_py()
        File "/tmp/pip-install-c2b4h0c7/realesrgan_fed472ef215948b384f45de0bf3525fb/setup.py", line 65, in write_version_py
          sha = get_hash()
        File "/tmp/pip-install-c2b4h0c7/realesrgan_fed472ef215948b384f45de0bf3525fb/setup.py", line 51, in get_hash
          raise ImportError('Unable to get git version')
      ImportError: Unable to get git version
      [end of output]
  
  note: This error originates from a subprocess, and is likely not a problem with pip.
error: metadata-generation-failed

× Encountered error while generating package metadata.
╰─> See above for output.

note: This is an issue with the package mentioned above, not pip.
hint: See above for details.
```

在使用pip install facexlib后重新install InvokeAI获得新报错
```shell
  Preparing metadata (setup.py) ... done
ERROR: Cannot install realesrgan because these package versions have conflicting dependencies.

The conflict is caused by:
    basicsr 1.4.2 depends on tb-nightly
    basicsr 1.4.1 depends on tb-nightly
    basicsr 1.4.0 depends on tb-nightly
    basicsr 1.3.5 depends on tb-nightly
    basicsr 1.3.4.9 depends on tb-nightly
    basicsr 1.3.4.8 depends on tb-nightly
    basicsr 1.3.4.7 depends on tb-nightly
    basicsr 1.3.4.6 depends on tb-nightly
    basicsr 1.3.4.4 depends on tb-nightly
    basicsr 1.3.4.3 depends on tb-nightly
    basicsr 1.3.4.2 depends on tb-nightly
    basicsr 1.3.4.1 depends on tb-nightly
    basicsr 1.3.4.0 depends on tb-nightly
    basicsr 1.3.3.11 depends on tb-nightly
    basicsr 1.3.3.10 depends on tb-nightly
    basicsr 1.3.3.9 depends on tb-nightly
    basicsr 1.3.3.8 depends on tb-nightly
    basicsr 1.3.3.7 depends on tb-nightly
    basicsr 1.3.3.6 depends on tb-nightly
    basicsr 1.3.3.5 depends on tb-nightly
    basicsr 1.3.3.4 depends on tb-nightly
    basicsr 1.3.3.3 depends on tb-nightly
    basicsr 1.3.3.1 depends on tb-nightly
    basicsr 1.3.3 depends on tb-nightly
    basicsr 1.3.2 depends on tb-nightly
    basicsr 1.3.1 depends on tb-nightly

To fix this you could try to:
1. loosen the range of package versions you've specified
2. remove package versions to allow pip attempt to solve the dependency conflict

ERROR: ResolutionImpossible: for help visit https://pip.pypa.io/en/latest/topics/dependency-resolution/#dealing-with-dependency-conflicts

```

