---
title:  "How to set up Mujoco on Greene"
mathjax: true
layout: post
categories: media
---

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
