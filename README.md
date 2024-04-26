# Instructions to postprocess FMS history output on PP/AN or gaea

1. Clone postprocessing template repository

```
module load fre/canopy
fre pp checkout -e EXPNAME -p PLATFORM -t TARGET
```

2. Configure pp template with either XML or pp.yaml

```
fre pp convert -e EXPNAME -p PLATFORM -t TARGET -x XML

or

fre pp configure -e EXPNAME -p PLATFORM -t TARGET -y YAML 

```

3. Validate the configuration

```
fre pp validate -e EXPNAME -p PLATFORM -t TARGET
```

Warnings related to directories are probably valid and should be fixed in rose-suite.conf.

If you are attempting this on gaea, you'll need to change the SITE variable in rose-suite.conf from `ppan` to `gaea`.

4. Install the workflow

```
fre pp install -e EXPNAME -p PLATFORM -t TARGET
```

If you are attempting this on gaea, you'll need to make two one-time changes before installing.
- Currently, cylc, rose, and isodatetime must be in your PATH for new shells. One approach to do this is
to symlink the fms-user-installed fre-cli cylc/rose/isodatetime scripts into your local ~/bin directory,
and then add that ~/bin directory to your PATH in your .bashrc or .cshrc. (If you don't do this, Cylc tasks
will fail complaining those 3 tools are not available.)

```
cd ~/bin
ln -s /ncrc/home2/Flexible.Modeling.System/conda/envs/fre-cli/bin/{cylc,rose,isodatetime} .
echo 'setenv PATH ${PATH}:~/bin' >> ~/.cshrc
```
- Currently, the cylc available on gaea (through "module load cylc" or the PATH trick above) does not
include any global configuration, so you'll need to create a file `~/.cylc/flow/global.cylc` that contains the following.
If you don't do this, Cylc will use your home directory for the scratch space and rapidly fill your quota.)

```
[install]
    [[symlink dirs]]
        [[[localhost]]]
            run = /gpfs/f5/scratch/gfdl_f/$USER
```

5. Run the workflow

```
fre pp run -e EXPNAME -p PLATFORM -t TARGET
```

6. Report status of workflow progress

```
fre pp status -e EXPNAME -p PLATFORM -t TARGET
```

7. Launch GUI

```
TODO: fre pp gui?

The full GUI can be launched on jhan or jhanbigmem (an107 or an201).

cylc gui --ip=`hostname -f` --port=`jhp 1` --no-browser
```
