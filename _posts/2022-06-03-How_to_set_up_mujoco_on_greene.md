---
title:  "How to set up Mujoco and Polyfempy on Greene and Singularity"
mathjax: true
layout: post
categories: media
---

# Set up Mujoco and Mujoco_py on Greene
## Approach One
To set up mujoco on Greene HPC, the original tutorial is no longer working from [Original Tutorial](https://www.notion.so/Setting-up-Mujoco-and-MPI-on-Greene-0fad2a6ac9e54115960f57a69e6ba6dd). The basic steps are still the same, but there are a few minor changes.
* Steps
1. Set up you own Singularity image and overlay according to [Singularity Tutorial](https://github.com/nyu-dl/cluster-support/tree/master/greene).
2. Download mujoco200 or mujoco210(Newest version now) linux and unzip it inside ~/.mujoco
3. If you are using mujoco200, please set go to [Mjkey](https://www.roboti.us/license.html) and click on Activation key and download it. Then put the key to the bin subdirectory of your MuJoCo installation(e.g. ~/.mujoco/mujoco200/bin). If you are using mujoco210, then there's no need to download the mjkey file. Just use it.
4. copy 
   ```
   /scratch/work/public/singularity/mujoco200-dep-cuda10.1-ubuntu18.04-20201207.sqf 
   ```
   to 
   ```
   /scratch/$USER/<workdir>
   ``` 
   Or you can use any mujoco*.sqf files in folder ```/scratch/work/public/singularity/``` as long as it matches the cuda version you choose and the OS version in your singularity.
   Note that there's no more any mujoco*.ext3 file system but only *sqf (Squashed file system) file. You can use these *.sqf file equally as *.ext3 file.
5. Now you can set up your singularity.sh file
    {% highlight bash %}
        #!/bin/bash

        # https://stackoverflow.com/questions/1668649/how-to-keep-quotes-in-bash-arguments
        args=''
        for i in "$@"; do
            i="${i//\\/\\\\}"
            args="$args \"${i//\"/\\\"}\""
        done

        if [ "$args" == "" ]; then args=/bin/bash; fi

        module purge

        export PATH=/share/apps/singularity/bin:$PATH

        # file systems
        export SINGULARITY_BINDPATH=/mnt,/scratch
        if [ -d /state/partition1 ]; then
            export SINGULARITY_BINDPATH=$SINGULARITY_BINDPATH,/state/partition1
        fi

        # SLURM related
        export SINGULARITY_BINDPATH=$SINGULARITY_BINDPATH,/opt/slurm,/usr/lib64/libmunge.so.2.0.0,/usr/lib64/libmunge.so.2,/var/run/munge,/etc/passwd
        export SINGULARITYENV_PREPEND_PATH=/opt/slurm/bin

        if [ -d /opt/slurm/lib64 ]; then
            export SINGULARITY_CONTAINLIBS=$(echo /opt/slurm/lib64/libpmi* | xargs | sed -e 's/ /,/g')
        fi

        if [[ $(hostname -s) =~ ^g ]]; then nv="--nv"; fi

        singularity exec $nv \
                --overlay /scratch/$USER/<workdir>/<your_overly_file_system>.ext3:ro \
                --overlay /scratch/$USER/<workdir>/mujoco<your_sqf_file>.sfq:ro \
                /scratch/work/public/singularity/cuda11.0-cudnn8-devel-ubuntu18.04.sif \
                /bin/bash -c "
        source /ext3/env.sh
        conda activate <your environment>
        $args
        "
    {% endhighlight %}

## Some other tips on Greene
If you want to upgrade CMake or GCC on you singularity, use conda to install them. 
1. Install CMake `conda install -c anaconda cmake`([https://anaconda.org/anaconda/cmake](https://anaconda.org/anaconda/cmake))
2. Install GCC `conda install -c conda-forge gcc` ([https://anaconda.org/conda-forge/gcc](https://anaconda.org/conda-forge/gcc))

## Approach Two
In approach one, we included overlay file system `--overlay /scratch/$USER/<workdir>/mujoco<your_sqf_file>.sfq:ro`. In this file system, an /ext3 folder, /ext3/miniconda3 folder and /ext3/env.sh have already existed because these are required to set up mujoco in Singluarity. However, since this file system is read only, you cannot install or creat new conda environments in `ext3/miniconda3/envs` because you have no permission to modify this folder. The only option is create your own ~/.condarc to set new `envs_dirs` and `pkgs_dirs`. In approach 2, we can build mujoco from scratch. Here's how we do it:
We choose Singularity OS image: /scratch/work/public/singularity/cuda11.1.1-cudnn8-devel-ubuntu20.04.sif with cuda support:
* Steps
  1. ```
        cp -rp /scratch/work/public/overlay-fs-ext3/overlay-50GB-10M.ext3.gz .
        gunzip overlay-15GB-500K.ext3.gz
        mv overlay-15GB-500K.ext3 ipc.ext3 
        ```
        
        Create your own file system(In my case, I am also creating the file system for ipc, so I rename my file system to ipc.ext3)

  2. Launch Singularity interactivly, bind `/share/apps` in order to use shell script `/share/apps/utils/singularity-conda/setup-conda.bash`
   
        ```
        singularity \
        exec --nv \
        --bind /share/apps \
        --bind /usr/share/glvnd/egl_vendor.d/50_mesa.json \
        --overlay <path-to-working-dir>/ipc.ext3 \
        /scratch/work/public/singularity/cuda11.1.1-cudnn8-devel-ubuntu20.04.sif \
        /bin/bash
        ```

        Here, `--bind /usr/share/glvnd/egl_vendor.d/50_mesa.json` is to include the `<GL/osmesa.h>` during installation of mujoco_py.
    1. Get Mujoco210 and unzip it
        ```
        cd /ext3
        wget https://github.com/deepmind/mujoco/releases/download/2.1.0/mujoco210-linux-x86_64.tar.gz
        tar -vxzf mujoco210-linux-x86_64.tar.gz
        ```
    2. We also need a copy inside ~/.mujoco because some packages installtion will use the header files there
        ```
        mkdir -p ~/.mujoco
        cd ~/.mujoco
        wget https://github.com/deepmind/mujoco/releases/download/2.1.0/mujoco210-linux-x86_64.tar.gz
        tar -vxzf mujoco210-linux-x86_64.tar.gz
        ```
    3. To setup miniconda with wrapper script
        ```
        cd /ext3
        bash /share/apps/utils/singularity-conda/setup-conda.bash 
        ```
        Now miniconda is ready.
    4.  Enable conda by `source /ext3/env.sh`
    5.  Create your own conda environment. In my case, I will be using the `environment.yml` from [IPC-Robotics](https://github.com/arvigj/ipc-robotics/tree/duo_edit).
        ```
        conda env create -f environment.yml
        ```
    6. Activate your environment `conda activate ipc-robotics`
    7. Download mujoco-py 2.1.2.14 to anywhere you want and unzip it:
        ```
        wget https://github.com/openai/mujoco-py/archive/refs/tags/v2.1.2.14.tar.gz
        tar -vxzf v2.1.2.14.tar.gz
        ```
    8.  Modify the file mujoco-py-2.1.2.14/mujoco_py/builder.py and add these lines after line 30 for Singularity, current implementation only supports docker and host.
        ```
        singularity_path = '/.singularity.d/libs'
        if exists(singularity_path) :
            return singularity_path 
        ```
        which makes the original function look like:
        {% highlight python %}
            def get_nvidia_lib_dir():
                exists_nvidia_smi = subprocess.call("type nvidia-smi", shell=True,
                                        stdout=subprocess.PIPE, stderr=subprocess.PIPE) == 0
                if not exists_nvidia_smi:
                    return None
                
                singularity_path = '/.singularity.d/libs'
                if exists(singularity_path) :
                    return singularity_path 
            
                docker_path = '/usr/local/nvidia/lib64'
                if exists(docker_path):
                    return docker_path

                nvidia_path = '/usr/lib/nvidia'
                if exists(nvidia_path):
                    return nvidia_path

                paths = glob.glob('/usr/lib/nvidia-[0-9][0-9][0-9]')
                paths = sorted(paths)
                if len(paths) == 0:
                    return None
                if len(paths) > 1:
                    print("Choosing the latest nvidia driver: %s, among %s" % (paths[-1], str(paths)))

                return paths[-1]
                {% endhighlight %}
    9.  Then we can install mujoco_py
        ```
        export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/.mujoco/mujoco210/bin

        cd mujoco-py-2.1.2.14
        pip --no-cache-dir install . -I
        ```
    10. Now we can create the env file for mujoco_py.
        ```
        touch /ext3/mujoco.sh
        echo "export MUJOCO_GL=\"egl\"" >> /ext3/mujoco.sh
        echo "export MJLIB_PATH=/ext3/mujoco210/bin/libmujoco210.so" >> /ext3/mujoco.sh
        ```
        Now every time you start your singluarity, you need to 
        ```
        source /ext3/env.sh
        source /ext3/mujoco.sh
        ```
    Now Mujoco and Mujoco_py is set up on Greene.

# Set up Polyfempy in Singularity
We still need to use Singularity OS image: /scratch/work/public/singularity/cuda11.1.1-cudnn8-devel-ubuntu20.04.sif with cuda support. Note that any other ubuntu image whose version is lower than 20.04 will have potential problems.

* Steps:
    1. Use the file system created above to start singularity interactivly:
        ```
        singularity \
        exec --nv \
        --bind /share/apps \
        --bind /usr/share/glvnd/egl_vendor.d/50_mesa.json \
        --overlay <path-to-working-dir>/ipc.ext3 \
        /scratch/work/public/singularity/cuda11.1.1-cudnn8-devel-ubuntu20.04.sif \
        /bin/bash
        ```
    2. Enable conda
        ```
        source /ext3/env.sh
        ```
    3. Activate your environment
        ```
        conda activate ipc-robotics
        ```
    4. Use conda to install higher version of CMake
        ```
        conda install -c anaconda cmake
        ```
    5. Get polyfempy from source:
        ```
        git clone git@github.com:polyfem/polyfem-python.git
        cd polyfem-python
        ```
    6. Install polyfempy from source 
        ```
        python setup.py install
        ```
    7. Waiting for installation to finish
    8. We can do some tests:
        ```
        python
        >>> import polyfempy as pf
        >>> solver = pf.Solver()
        >>> solver.set_log_level(3)
        ```
        

<!-- ## MathJax

You can enable MathJax by setting `mathjax: true` on a page or globally in the `_config.yml`. Some examples:

[Euler's formula](https://en.wikipedia.org/wiki/Euler%27s_formula) relates the  complex exponential function to the trigonometric functions.

$$ e^{i\theta}=\cos(\theta)+i\sin(\theta) $$

The [Euler-Lagrange](https://en.wikipedia.org/wiki/Lagrangian_mechanics) differential equation is the fundamental equation of calculus of variations.

$$ \frac{\mathrm{d}}{\mathrm{d}t} \left ( \frac{\partial L}{\partial \dot{q}} \right ) = \frac{\partial L}{\partial q} $$

The [Schrödinger equation](https://en.wikipedia.org/wiki/Schr%C3%B6dinger_equation) describes how the quantum state of a quantum system changes with time.

$$ i\hbar\frac{\partial}{\partial t} \Psi(\mathbf{r},t) = \left [ \frac{-\hbar^2}{2\mu}\nabla^2 + V(\mathbf{r},t)\right ] \Psi(\mathbf{r},t) $$

## Code

Embed code by putting `{{ "{% highlight language " }}%}` `{{ "{% endhighlight " }}%}` blocks around it. Adding the parameter `linenos` will show source lines besides the code.

{% highlight c %}

static void asyncEnabled(Dict* args, void* vAdmin, String* txid, struct Allocator* requestAlloc)
{
    struct Admin* admin = Identity_check((struct Admin*) vAdmin);
    int64_t enabled = admin->asyncEnabled;
    Dict d = Dict_CONST(String_CONST("asyncEnabled"), Int_OBJ(enabled), NULL);
    Admin_sendMessage(&d, txid, admin);
}

{% endhighlight %}

## Gists

With the `jekyll-gist` plugin, which is preinstalled on Github Pages, you can embed gists simply by using the `gist` command:

<script src="https://gist.github.com/5555251.js?file=gist.md"></script>

## Images

Upload an image to the *assets* folder and embed it with `![title](/assets/name.jpg))`. Keep in mind that the path needs to be adjusted if Jekyll is run inside a subfolder.

A wrapper `div` with the class `large` can be used to increase the width of an image or iframe.

![Flower](https://user-images.githubusercontent.com/4943215/55412447-bcdb6c80-5567-11e9-8d12-b1e35fd5e50c.jpg)

[Flower](https://unsplash.com/photos/iGrsa9rL11o) by Tj Holowaychuk

## Embedded content

You can also embed a lot of stuff, for example from YouTube, using the `embed.html` include.

{% include embed.html url="https://www.youtube.com/embed/_C0A5zX-iqM" %} -->
