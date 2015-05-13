---
layout: post
title: "Improved and colored command prompt bash on OSX"
comments: true
categories: osx bash
---

When you start a standard OSX Terminal running bash you see that it is uncolored and boring as an old fashioned VAX terminal at university. So I decided to go with a new setting to make the overall terminal experience better, like those you see on various Linux distribution.<!--more-->

My desire was to have:

- improved and clear prompt information with user and current directory
- colored prompt
- colored ls
- rapid alias as "ll" to show all files (ls -la) 

My machine name is "settedinove" (that is the Italian translation of "Seven of Nine", the female borg fiction character from "Star Trek: Voyager") and this is an example terminal before applying the modification to it:

![OSX Terminal before treatment]({{site.baseurl}}/assets/images/2015-05-13-SS1.png)

Boring...

So let's proceed with creating a file ".bash_profile" in the root of your home folder:

You can use a text editor, I prefer to use VI from the command line as follows:

{% highlight sh %}
vi .bash_profile
{% endhighlight %}

or using nano:

{% highlight sh %}
nano .bash_profile
{% endhighlight %}

and copy the following content:

{% highlight sh %}
##################################################
# Fancy PWD display function
##################################################
# The home directory (HOME) is replaced with a ~
# The last pwdmaxlen characters of the PWD are displayed
# Leading partial directory names are striped off
# /home/me/stuff          -> ~/stuff               if USER=me
# /usr/share/big_dir_name -> ../share/big_dir_name if pwdmaxlen=20
##################################################
bash_prompt_command() {
            # How many characters of the $PWD should be kept
            local pwdmaxlen=25
            # Indicate that there has been dir truncation
            local trunc_symbol=".."
            local dir=${PWD##*/}
            pwdmaxlen=$(( ( pwdmaxlen < ${#dir} ) ? ${#dir} : pwdmaxlen ))
            NEW_PWD=${PWD/$HOME/~}
            local pwdoffset=$(( ${#NEW_PWD} - pwdmaxlen ))
            if [ ${pwdoffset} -gt "0" ]
                then
                NEW_PWD=${NEW_PWD:$pwdoffset:$pwdmaxlen}
                NEW_PWD=${trunc_symbol}/${NEW_PWD#*/}
            fi
}
#
bash_prompt() {
    local NONE='\[\033[0m\]'    # unsets color to term's fg color

        # regular colors
        local K='\[\033[0;30m\]'    # black
        local R='\[\033[0;31m\]'    # red
        local G='\[\033[0;32m\]'    # green
        local Y='\[\033[0;33m\]'    # yellow
        local B='\[\033[0;34m\]'    # blue
        local M='\[\033[0;35m\]'    # magenta
        local C='\[\033[0;36m\]'    # cyan
        local W='\[\033[0;37m\]'    # white

        # empahsized (bolded) colors
        local EMK='\[\033[1;30m\]'
        local EMR='\[\033[1;31m\]'
        local EMG='\[\033[1;32m\]'
        local EMY='\[\033[1;33m\]'
        local EMB='\[\033[1;34m\]'
        local EMM='\[\033[1;35m\]'
        local EMC='\[\033[1;36m\]'
        local EMW='\[\033[1;37m\]'

        # background colors
        local BGK='\[\033[40m\]'
        local BGR='\[\033[41m\]'
        local BGG='\[\033[42m\]'
        local BGY='\[\033[43m\]'
        local BGB='\[\033[44m\]'
        local BGM='\[\033[45m\]'
        local BGC='\[\033[46m\]'
        local BGW='\[\033[47m\]'

        local UC=$C                 # user's color
        [ $UID -eq "0" ] && UC=$R   # root's color

        #PROMPT_COMMAND='echo -ne "\033]0;${USER}@${HOSTNAME}: ${PWD}\007"'
        #PS1="${EMK}[${UC}\u${EMR}@${UC}\h ${EMB}\${NEW_PWD}${EMK}]${UC}\\$ ${NONE}"
        PS1="${W}[\t${W}] ${Y}[${R}\u${R}@${R}\h ${Y}\${NEW_PWD}${Y}]${W}\\$ ${NONE}"
}
#
PROMPT_COMMAND=bash_prompt_command
export CLICOLOR=1
export LSCOLORS=GxFxCxDxBxegedabagaced
bash_prompt
unset bash_prompt

alias ll='ls -lGh $@'

# if you need any path on your bash environment, add  an export command as the following example:
export PATH=$PATH:~/gradle-2.3/bin
{% endhighlight %}

Now you have to load your configuration (or exit and open again the Terminal):

{% highlight sh %}
source .bash_profile
{% endhighlight %}

or

{% highlight sh %}
. .bash_profile
{% endhighlight %}


This is how it appears after the treatment:

![OSX Terminal after treatment]({{site.baseurl}}/assets/images/2015-05-13-SS2.png)

You can do "ll" to show the contents of a directory, add any alias command you like in .bash_profile, improving the overall shell experience.

Feel free to share improvements in the comments below.

Dino.

