<!--
.. title: SSH Key的使用
.. slug: ssh-keyde-shi-yong
.. date: 2015-04-20 18:47:24 UTC+08:00
.. tags:
.. category:
.. link:
.. description:
.. type: text
-->

#### 進入ssh目錄

首先先到個人的 ssh目錄
``` bash
$ cd ~/.ssh
```
如果沒有這個目錄, 需手動建一個
``` bash
$ mkdir .ssh
```

#### 建立新的 SSH key
產生一個新的SSH key, 可以使用下列指令
``` bash
$ ssh-keygen -t rsa
```
依照提示, 輸入你的密碼後,就可以在 `.ssh`目錄下看到 `id_rsa`及`id_rsa.pub`,這二個檔.`id_rsa.pub` 就是公鑰, 而

<!-- TEASER_END -->

#### 將公鑰放入主機
先把公鑰放到主機
``` bash
$ scp ~/.ssh/*.pub [目的主機]:~/.ssh/.
```
登入主機
``` bash
$ ssh [username]@[目的主機]
```
把公鑰存到主機. 系統預設處理公鑰的檔案為`authorized_keys`,這個檔案必須把權限設為只有使用者具有寫的權限
``` bash
$ cat ~/.ssh/identity.pub >> ~/.ssh/authorized_keys
$ chmod 711 ~/.ssh
$ chmod 644 ~/.ssh/authorized_keys
```



#### 設定 `ssh-agent`
起動 ssh-agent
``` bash
$ eval `ssh-agent -s`
```
新增 key
``` bash
$ ssh-add
```

#### 自動化執行
參考[Joseph M. Reagle Jr.](http://www.cygwin.com/ml/cygwin/2001-06/msg00537.html "useful cygwin ssh-agent script")

放在 .bash_profile 的 script


SH_ENV="$HOME/.ssh/environment"


        function start_agent {
           echo "Initialising new SSH agent..."
           /usr/bin/ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
           echo succeeded
           chmod 600 "${SSH_ENV}"
           . "${SSH_ENV}" > /dev/null
        /usr/bin/ssh-add;
        }

        # Source SSH settings, if applicable

        if [ -f "${SSH_ENV}" ]; then
           . "${SSH_ENV}" > /dev/null
           #ps ${SSH_AGENT_PID} doesn’t work under cywgin
           ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
           start_agent;
       }
        else
         start_agent;
        fi


#### 使用 ssh config 來對映多個ssh 帳號

編輯`~/.ssh/config`, 如果沒有就建一個. 文件格式為
```
        Host [名稱]
            HostName [主機名]
            User [使用者登入ID]
            IdentityFile [私鑰路徑]
```
