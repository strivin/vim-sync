vim-sync
========

Automatic sync local and remote file in vim


Installation
----

Install using [bundle],[vundle],[pathogen] or your favorite Vim package manager.

Usage
----

create a execute file called .sync in your project directory.
e.g. /project_dir/ is your project directory and the execute file is /project_dir/.sync. how to write execute file see "some execute config" section.

    <leader>su
    Upload current buffer file, it will execute the command: project_dir/.sync upload current_buffer_fold current_file_name
    
    <leader>sd
    Download current buffer file, it will execute the command: project_dir/.sync download current_buffer_fold current_file_name

    <leader>sr
    Sync the project_dir to remote, it will execute the command: project_dir/.sync sync_to_remote current_buffer_fold current_file_name

    <leader>sl
    Sync the project_dir to local, it will execute the command: project_dir/.sync sync_to_local current_buffer_fold current_file_name

some execute config
----
* scp:
```sh
#!/bin/sh
######user define begin#######
username="myuser"
password="mypassword"
host="192.168.1.1"
remote_path="/home/test"
######user define end#########

project_path=`dirname $0`
local_path=${project_path%/*}
project_name=${project_path##*/}
file_path=$2
file_name=$3
#echo "***************"
#echo "project_path:" $project_path
#echo "local_path:"   $local_path
#echo "project_name:" $project_name
#echo "file_path:"    $file_path
#echo "file_name:"    $file_name
#echo "***************"

if [ 'upload' == $1 ];then
    #when the file in the root of projetc_path, the project_path equel file_path,
    #so need the special case
    if [ $project_path == $file_path ];then
        cmd="$file_path/$file_name $username@$host:$remote_path/$project_name/"
    else
        cmd="$project_path/$file_path/$file_name $username@$host:$remote_path/$project_name/$file_path/"
    fi
elif [ 'download' == $1 ];then
    if [ $project_path == $file_path ];then
        cmd="$username@$host:$remote_path/$project_name/$file_name $file_path/"
    else
        cmd="$username@$host:$remote_path/$project_name/$file_path/$file_name $project_path/$file_path/"
    fi
elif [ 'sync_to_remote' == $1 ];then
    cmd="$local_path/$project_name $username@$host:$remote_path/"
elif [ 'sync_to_local' == $1 ];then
    cmd="$username@$host:$remote_path/$project_name $local_path/"
fi

sshpass -p $password scp -q -r $cmd
```
<pre>
e.g. directory structer
home
   └── test
       └── project_dir
           ├── include
           │   ├── bar.h
           │   └── foo.h
           ├── README.md
           ├── src
           │   ├── bar
           │   │   └── bar.c
           │   └── foo
           │       └── foo.c
           └── .sync

project_path is /home/test/project_dir
local_path is   /home/test
project_name is project_dir
file_path is the file's relative path (e.g. for file bar.h/foo.h, the file_path is include.
and for bar.c, the file_path is src/bar)

note: when the file you are editing in the root of project_dir, the project_path is the same with the file_path
e.g. for README.md
the project_path and the file_path are also /home/test/project_dir, so we need to special process.
</pre>
* ...

Alias
----
  
If you want to another command, write following like.

Ctrl+u  
    `nnoremap <C-U> <ESC>:call SyncUploadFile()<CR>`
    
Ctrl+d  
    `nnoremap <C-U> <ESC>:call SyncDownloadFile()<CR>`
    
* if you want to auto upload/download file where save/open file, write these code in you .emacs config file:
 
        autocmd BufWritePost * :call SyncUploadFile()
        autocmd BufReadPre * :call SyncDownloadFile()

    
[bundle]:https://github.com/bundler/bundler/
[vundle]:https://github.com/gmarik/vundle/
[pathogen]:https://github.com/tpope/vim-pathogen/

