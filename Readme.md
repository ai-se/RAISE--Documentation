# A Quick Guide to Setting Up The HPCC Clusters
## Installing Python2.7: 
1. Login to the HPC cluster using ```ssh -X <unity-id>@login01.hpc.ncsu.edu```
2. Navigate to `/share`, then into your faculty folder name, and create a folder. __Note: Use your unity ID as the directory name to avoid filename conflicts.__
3. Now go into your folder and download the latest version of [miniconda](http://conda.pydata.org/miniconda.html): 
  ```wget http://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh``` for python2.7 or ```wget http://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh``` for python 3.6
  and install it ```bash Miniconda-latest-Linux-x86_64.sh```/```bash Miniconda3-latest-Linux-x86_64.sh```. __Note: You've gotta install this in your share space. Do not, I repeat, DO NOT install this in your $HOME directory. You will run out of disk space. Watch out for the part during the installation when it asks where to install this, say `./miniconda` (or `/share/<your-unity-id>/miniconda`)instead on the default which is `/home/<unity-id>/miniconda` (see figure below)__

  ![](_imgs/where2install.png)

4. These Miniconda installers contain the 'conda' package manager. Once Miniconda is installed, you can use the conda command to install any other packages and create environments, etc. But to do this you must export this to your path. Delete the shell script to save space after installation.

### Adding Miniconda to your path and installing python packages using `conda`
1. Go back to home directory; create a file called `.tcshrc`; 
   Add this line to the file:
   ```set path= ($path /share/<unity-id>/miniconda/bin/ $home/bin/)```
2. An optional (but strogly recommended) step would be to create an alias for python2.7 by adding the following lines to .tcshrc `alias python "python2.7"` so when you call python, it calls the one used by miniconda.
3. Logout and log back in, if you haven't already done so. Now you should be able to install all the packages using `conda install <package-name>`

## Installing git
1. Go to your home directory and create and directory called tmp. cd to tmp and download the git 2.3.3 tarball using ```wget --no-check-certificate https://www.kernel.org/pub/software/scm/git/git-2.3.3.tar.gz```
2. Untar using ```tar xfvz git-2.3.3.tar.gz``` and cd to git-2.3.3.
3. Run `make` and then run `make install`
4. Git is installed in `$HOME/bin` by default. So add that to your environment variable in `.tcshrc`. 
5. Also, you'll need to turn off SSL Verification to be able to run git so add `alias git "env GIT_SSL_NO_VERIFY=true git"` to your `.tcshrc`. 
6. At this point, your `.tcshrc` should look as follows:

  ```
  set path= ($path /share/<unity-id>/miniconda/bin/ $home/bin/)
  alias git "env GIT_SSL_NO_VERIFY=true git"
  alias python "python2.7"
  ```

7. Log out and log back in and you're all set.

# Submitting Jobs
1. As an example, I'll use my [run.py](https://github.com/ai-se/Transfer-Learning/blob/master/SOURCE/run.py) file. On an ordinary unix system, you would have to execute `$ python run.py _test ant > log/ant.py` to get it going.
2. Create 2 directories `out`(for outputs) and `err`(to store the error logs). 
3. To run this on the cluster create a bsub script as - 

`#!/bin/csh

 #BSUB -W 10
 
 #BSUB -n 1
 
 #BSUB -o out.%J
 
 #BSUB -e err.%J

 module load python
 
 python run.py`
 
 and save it as run.csh. Then to submit the job run the command `bsub < run.csh`.
 
 alternately(not desired) you can directly submit job from command line.
 
`bsub -W 6000 -n 4 -o ./out/out.%J -e ./err/err.%J python run.py _test ant > log/ant.log`
  - "-W 6000" asks for six thousand minutes of time. The job will time out after 100 hours if still running.
  - "-n 4" asks for 4 processors.
  - "-o ./out/out.%J" denotes a file where standard output from the job will be saved.
  - The "-e" line designates a file where standard error output from the job will be saved.
  - %J by the way assigns a unique process ID for the job. Use this to keep track of stuff.

### Multiple Jobs 
To submit multiple jobs, like in my case with 12 more projects like ant, use SHELL scripts to run your job. Create a master shell script as follows and just run that..
```shell
#! /bin/tcsh

rm err/* # Flush error logs
rm out/* # Flush output logs
foreach VAR ("ant" "camel" "forrest" "ivy" "jedit" "pbeans" "log4j" "synapse" "velocity" "xalan" "xerces")
  bsub -W 6000 -n 4 -o ./out/$VAR.out.%J -e ./err/$VAR.err.%J python run.py _test "$VAR" > log/"$VAR".log
end
```

## So, can it get any more geeky?
Why, yes of course! There are ways you can submit/monitor/kill your jobs on-the-fly using your smartphone and (or) tablet. While on campus, just install the apps listed below and you should be good to go. When not on campus, install and run the VPN service before using the apps (a detailed instruction on setting up your vpn is available [here](http://oit.ncsu.edu/data-network/ncsu-ssl-vpn-instructions)). Needless to say, the same goes for other devices operated outside NCSU.
### Android
 - Setup VPN if using from outside campus (see [here](http://oit.ncsu.edu/data-network/vpn-installation-instructions-android-devices))
 - Install [JuiceSSH](https://play.google.com/store/apps/details?id=com.sonelli.juicessh)
 - Go to connections.

    <img src="https://lh3.ggpht.com/86E2tjG3xFJPkuiHT53tJ-QQxKMzW3xcWAfGkVt80leVvh-yPMgdbSgp3ScO_bPzFAwe=h900-rw" width = 300px height = 500px>
 
 - Fill in the credentials and you should be good to go.
    ```
    Address: login64.hpc.ncsu.edu
    Username: <your-unity-ID>
    Password: <your-password>
    Port: 22 (optional)
    ```

### iOS
 - Again, VPN
 - Install [Serverauditor](https://itunes.apple.com/us/app/serverauditor-ssh-shell-console/id549039908?mt=8)
 - Add the connection, use the same credentials as above.


## If you plan on using vim
Create a .vimrc file in at the $HOME direction. Paste the following script to make it pretty (and useable).

```shell
" " Automatic reloading of .vimrc" 
autocmd! bufwritepost .vimrc source %
"
"
" " Better copy & paste
" " When you want to paste large blocks of code into vim, press F2 before you
" " paste. At the bottom you should see ``-- INSERT (paste) --``.
"
set pastetoggle=<F2>
set clipboard=unnamed
"
"
" " Mouse and backspace
set mouse=a  " on OSX press ALT and click
set bs=2     " make backspace behave like normal again
"
"
" " Rebind <Leader> key
" " I like to have it here becuase it is easier to reach than the default and
" " it is next to ``m`` and ``n`` which I use for navigating between tabs.
let mapleader = ","
"
"
" " Bind nohl
" " Removes highlight of your last search
" " ``<C>`` stands for ``CTRL`` and therefore ``<C-n>`` stands for ``CTRL+n``
noremap <C-n> :nohl<CR>
vnoremap <C-n> :nohl<CR>
inoremap <C-n> :nohl<CR>
"
"

" Quicksave command
noremap <C-Z> :update<CR>
vnoremap <C-Z> <C-C>:update<CR>
inoremap <C-Z> <C-O>:update<CR>


" Quick quit command
noremap <Leader>e :quit<CR>  " Quit current window
noremap <Leader>E :qa!<CR>   " Quit all windows


" bind Ctrl+<movement> keys to move around the windows, instead of using Ctrl+w + <movement>
" Every unnecessary keystroke that can be saved is good for your health :)
map <c-j> <c-w>j
map <c-k> <c-w>k
map <c-l> <c-w>l
map <c-h> <c-w>h

" " easier moving between tabs
map <Leader>n <esc>:tabprevious<CR>
map <Leader>m <esc>:tabnext<CR>
"
"
" Sort
vnoremap <Leader>s :sort<CR>
"
"
" easier moving of code blocks
" Try to go into visual mode (v), thenselect several lines of code here and
" " then press ``>`` several times.
vnoremap < <gv  " better indentation
vnoremap > >gv  " better indentation
"
"
" " Show whitespace
" " MUST be inserted BEFORE the colorscheme command
" autocmd ColorScheme * highlight ExtraWhitespace ctermbg=red guibg=red
" au InsertLeave * match ExtraWhitespace /\s\+$/
"
"
" " Color scheme
" " mkdir -p ~/.vim/colors && cd ~/.vim/colors
" " wget -O wombat256mod.vim
" http://www.vim.org/scripts/download_script.php?src_id=13400
set t_Co=256
" color wombat256mod
"
"
" " Enable syntax highlighting
" " You need to reload this file for the change to apply
filetype off
filetype plugin indent on
syntax on
"
"
" " Showing line numbers and length
set number  " show line numbers
set tw=78   " width of document (used by gd)
set nowrap  " don't automatically wrap on load
set fo-=t   " don't automatically wrap text when typing
" set colorcolumn=10
" highlight ColorColumn ctermbg=233"

" easier formatting of paragraphs
vmap Q gq
nmap Q gqap
"
"
" Useful settings
set history=700
set undolevels=700
"
"
" " Real programmers don't use TABs but spaces
set tabstop=2
set softtabstop=2
set shiftwidth=2
set shiftround
set expandtab
"
"
" " Make search case insensitive
set hlsearch
set incsearch
set ignorecase
set smartcase
"
"
" " Disable stupid backup and swap files - they trigger too many events
" " for file system watchers
set nobackup
set nowritebackup
set noswapfile
"
```
