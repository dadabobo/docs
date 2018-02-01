devconfg.md


#### bash_profile
```bash
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/.local/bin:$HOME/bin
export PATH

# Tomcat
export CATALINA_HOME=/opt/soft/apache-tomcat-7.0.67

# set maven environment
export M2_HOME=/opt/soft/apache-maven-3.3.9
export MAVEN_OPTS="-Xms128m -Xmx512m"

# set zookeeper environment
export ZOO_LOG_DIR=~/data/zkdata


# set app path: strom/tomcat/zookeeper/marven
PATH=$PATH:/opt/soft/apache-storm-0.10.0/bin:$CATALINA_HOME/bin:/opt/soft/zookeeper-3.4.7/bin
PATH=$PATH:$M2_HOME/bin
export PATH
```


#### tnsname.ora
```
#+TITLE: 常用 oracle 数据库连接信息

* 本机数据库
XE =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 10.21.49.229)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = XE)
    )
  )

# Win10-xe (NUC)
NUCXE =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.0.101)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = XE)
    )
  )

# Win10-xe (DELL)
NBXE =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 10.21.49.229)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = XE)
    )
  )

* 开发数据库
# 公司开发数据库 (new billing)
NBILLING =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 10.21.20.76)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = nbilling)
    )
  )

# ??
NB = 
  (DESCRIPTION = 
    (ADDRESS_LIST = 
      (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.15.201)(PORT = 1521)) 
    ) 
    (CONNECT_DATA = 
      (SERVICE_NAME = nbilling) 
    ) 
  ) 

# ??
yn_actdev =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.5.48)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = ynactdev)
    )
  )
    
* 云南测试库
yn_bilact = 
  (DESCRIPTION =
    (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = 10.173.246.92)(PORT = 1521)))
    (CONNECT_DATA = (SERVICE_NAME = bilact))
  )
 
yn_bilbil =
  (DESCRIPTION =
    (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = 10.173.246.90)(PORT = 1521)))
    (CONNECT_DATA = (SERVICE_NAME = bilbil))
  )
    
* 云南并行数据库
TBILDB1 =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 10.173.253.155)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = tbildb1)
    )
  )
  
TBILDB2 =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 10.173.253.156)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = tbildb2)
    )
  )  

TACTDB1 =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 10.173.252.26)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = tactdb1)
    )
  )

TACTDB2 =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 10.173.252.27)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = tactdb2)
    )
  )

TBCEN =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 10.173.252.28)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = tbcen)
    )
  )
```


#### .gitconfig
```
[user]
    name = Wang Weiguo
    email = wangwg2@msn.com

[core]
    editor = emacs
[credential]
    helper = cache
```


#### .emacs
```
;;=======================================================================  
;; ~/.emacs
;; author: wangwg 
;; e-mail: wangwg2@msn.com
;; update:2015-10-29  
;;=======================================================================  

;; 一打开就起用 text 模式。 
(setq default-major-mode 'text-mode)

;; 语法高亮
(global-font-lock-mode t)

;; 以 y/n代表 yes/no
(fset 'yes-or-no-p 'y-or-n-p)

;;;;设置备份
;;不产生备份文件  
(setq make-backup-files nil)  
;;不生成临时文件  
;(setq-default make-backup-files nil)


;; 显示括号匹配 
(show-paren-mode t)
(setq show-paren-style 'parentheses)

;; 显示时间，格式如下
(display-time-mode 1)
;(setq display-time-24hr-format t) 
;(setq display-time-day-and-date t) 

;; 默认显示 80列就换行 
;(setq default-fill-column 80) 

;; 显示列号
(setq column-number-mode t)
(setq line-number-mode t)


;;设置google-c-style
;;下载 http://google-styleguide.googlecode.com/svn/trunk/google-c-style.el
;; 到 ~/.emacs.d 目录下
;(add-to-list 'load-path (expand-file-name "~/.emacs.d"))
;(require 'google-c-style)
;(add-hook 'c-mode-common-hook 'google-set-c-style)
;(add-hook 'c-mode-common-hook 'google-make-newline-indent)



;;格式化整个文件函数
(defun indent-whole()
    "indent the whole buffer"
    (interactive)
    (save-excursion
        (indent-region (point-min) (point-max) nil))
    (message "format successfully"))
;;绑定到F7键
(global-set-key [f7] 'indent-whole)


;; 设置默认tab宽度为2

;;;;设置TAB宽度为2
(setq tab-width 2)
(setq default-tab-width 2)
(setq   indent-tabs-mode t)
(setq-default c-basic-offset 2)


(setq c-default-style '((java-mode . "java")
                                                (c-mode . "stroupstrup")
                                                (c++-mode ."stroupstrup")))

(setq c-toggle-auto-hungry-state 1) 


;;自动缩进
(add-hook 'c-mode-common-hook '(lambda () (c-toggle-auto-state 1)))


;;以下设置缩进  
;;用tab缩进  
;(setq c-indent-level 2)
;(setq c-continued-statement-offset 2)
;(setq c-brace-offset -2)
;(setq c-argdecl-indent 2)
;(setq c-label-offset -2)
;(setq c-basic-offset 2)
;(global-set-key "\C-m" 'reindent-then-newline-and-indent)


;; C language setting
;(add-hook 'c-mode-hook
;                   '(lambda ()
;                        (c-set-style "stroupstrup")
;                        (setq tab-width 2)
;                        (setq indent-tabs-mode t)
;                        (setq c-basic-offset 2)))

;; C++ language setting
;(add-hook 'c++-mode-hook
;                   '(lambda ()
;                        (c-set-style "stroupstrup")
                         ;;(c-toggle-auto-state)
;                        (setq tab-width 2)
;                        (setq indent-tabs-mode t)
;                        (setq c-basic-offset 2)))


;; my-c-style
;(defconst my-c-style
;   '((c-tab-always-indent        . t)
;       (c-comment-only-line-offset . 2)
;       (c-hanging-braces-alist     . ((substatement-open after)
;                                                                    (brace-list-open)))
;       (c-hanging-colons-alist     . ((member-init-intro before)
;                                                                    (inher-intro)
;                                                                    (case-label after)
;                                                                    (label after)
;                                                                    (access-label after)))
;       (c-cleanup-list             . (scope-operator
;                                                                    empty-defun-braces
;                                                                    defun-close-semi))
;       (c-offsets-alist            . ((arglist-close . c-lineup-arglist)
;                                                                    (substatement-open . 0)
;                                                                    (case-label        . 2)
;                                                                    (block-open        . 0)
;                                                                    (knr-argdecl-intro . -)))
;       (c-echo-syntactic-information-p . t)
;       )
;   "My C Programming Style")

;; offset customizations not in my-c-style
;(setq c-offsets-alist '((member-init-intro . ++)))




;; Customizations for all modes in CC Mode.
;(defun my-c-mode-common-hook ()
    ;; add my personal style and set it for the current buffer
;   (c-add-style "PERSONAL" my-c-style t)
    ;; other customizations
;   (setq tab-width 4
        ;; this will make sure spaces are used instead of tabs
;       indent-tabs-mode nil)
    ;; we like auto-newline and hungry-delete
;   (c-toggle-auto-hungry-state 1)
    ;; key bindings for all supported languages.  We can put these in
    ;; c-mode-base-map because c-mode-map, c++-mode-map, objc-mode-map,
    ;; java-mode-map, idl-mode-map, and pike-mode-map inherit from it.
;   (define-key c-mode-base-map "\C-m" 'c-context-line-break)
;   )
```