# My Emacs configuration

![img](./imgs/my-emacs.png)

This is my Emacs configuration, I use it for:

-   Programming languages (C, C++, Go, Javascript, Python, Lisp, Shell script)
-   Markup languages (Latex, HTML, CSS, Org)
-   Other languages (Makefile, yaml, dockerfile)
-   RSS
-   Media player
-   Window manager
-   File manager
-   Password manager
-   Wiki
-   Blog


# Installation

-   Copy init.el and settings.org to ~/.emacs.d/ or ~/.config/emacs


# Programming


## Magit

```emacs-lisp
(use-package magit
  :straight t
  :bind (("C-x g" . magit-status)))
```


## Company

Company is a text completion framework.

```emacs-lisp
(use-package company
  :straight t
  :commands company-mode
  :bind (:map company-active-map
	      ("C-n" . 'company-select-next)
	      ("C-p" . 'company-select-previous))
  :hook ((emacs-lisp-mode . company-mode)
	 (lisp-mode . company-mode)
	 (sly-mrepl-mode . company-mode)))

(use-package company-quickhelp
  :straight t
  :hook (company-mode . company-quickhelp-local-mode))
```


## Language Server Protocol

LSP gives Emacs IDE features.

```emacs-lisp
(use-package lsp-mode
  :straight t
  :hook
  ((c++-mode . lsp)
   (c-mode . lsp)
   (js-mode . lsp)
   (python-mode . lsp))
  :commands lsp)
```


## Projectile

```emacs-lisp
(use-package projectile
  :straight t
  :commands projectile-mode)
```


## Syntax checking

```emacs-lisp
(use-package flycheck
  :straight t
  :defer 4.3)
```


## Jump to definition

It's good to have two frameworks for jumping to definition (lsp also provides its own functions).


### Requirements

Silver searcher: <https://github.com/ggreer/the_silver_searcher>


### Config

```emacs-lisp
(use-package dumb-jump
  :straight t
  :config
  (setq dumb-jump-selector 'ivy)
  :commands dumb-jump-go)
```


## C and C++


### Requirements

-   Requires clang installed on the system.
-   Requires ccls installed on the system. <https://github.com/MaskRay/ccls>

I'm assuming that the ccls binary is at /usr/bin/ccls.


### Config

```emacs-lisp
(use-package ccls
  :after lsp-mode
  :straight t
  :config (setq ccls-executable "/usr/bin/ccls"))

(defun my-c-mode-common-hook ()
  (c-set-offset 'substatement-open 0)
  (c-set-offset 'access-label '/)
  (c-set-offset 'inclass '+)
  (setq c-default-style "bsd"
	c-basic-offset 4
	c-indent-level 4
	c-indent-tabs-mode t
	c-tab-always-indent t
	c++-tab-always-indent t
	tab-width 4
	backward-delete-function nil))

(add-hook 'c++-mode-common-hook 'my-c-mode-common-hook)
(add-hook 'c-mode-common-hook 'my-c-mode-common-hook)
```


## Golang


### Requirements

Autocompletion requires gocode, available at <https://github.com/nsf/gocode>. We can install gocode with go get, then we need to create a symbolic link at /usr/bin.


### Config

```emacs-lisp
(use-package go-mode
  :straight t
  :mode ("\\.go\\'" . go-mode)
  :config
  (defun my-go-mode-hook ()
    (setq tab-width 4)
    (setq gofmt-command "goimports")
    (set (make-local-variable 'company-backends) '(company-go))
    (company-mode))
  (add-hook 'go-mode-hook 'my-go-mode-hook))

(use-package company-go
  :after (company go-mode)
  :straight t)

(use-package go-errcheck
  :after go-mode
  :straight t)
```


## Lisp


### Config

```emacs-lisp
(use-package sly
  :straight t
  :defer 3.2
  :config
  (setq inferior-lisp-program "sbcl"))

(use-package suggest
  :straight t
  :commands suggest)

(use-package lispy
  :straight t
  :hook ((emacs-lisp-mode . lispy-mode)
	 (lisp-mode . lispy-mode)
	 (clojure-mode . lispy-mode)
	 (scheme-mode . lispy-mode)
	 (sly-mrepl-mode . lispy-mode)))
```


## Scheme

```emacs-lisp
(use-package geiser
  :straight t
  :commands run-geiser
  :config
  ;; I have to use the guile2.2 binary because of Fedora
  (setq geiser-guile-binary "guile2.2"))
```


## Clojure

```emacs-lisp
(use-package cider
  :straight t
  :commands cider-jack-in)
```


## Python

```emacs-lisp
(use-package elpy
  :straight t
  :hook (python-mode . elpy-enable)
  :config (setq elpy-rpc-backend "jedi"))

(use-package lpy
  :straight t
  :hook (python-mode . lpy-mode))
```


## Haskell

```emacs-lisp
(use-package haskell-mode
  :straight t
  :mode ("\\.hs\\'" . haskell-mode))
```


## Web


### HTML/CSS

```emacs-lisp
(use-package emmet-mode
  :straight t
  :hook ((web-mode . emmet-mode)
	 (css-mode . emmet-mode)))

(use-package web-mode
  :straight t
  :mode (("\\.html\\'" . web-mode)))

(use-package rainbow-mode
  :straight t
  :hook ((org-mode . rainbow-mode)
	 (web-mode . rainbow-mode)))

(use-package impatient-mode
  :straight t
  :commands impatient-mode)
```


### HTTP

```emacs-lisp
(use-package simple-httpd
  :straight t
  :commands httpd-start)

(use-package request
  :straight t
  :commands request)
```


### JS

```emacs-lisp
(use-package js2-mode
  :straight t
  :mode ("\\.js\\'" . js2-mode))

(use-package tide
  :straight t
  :hook (js-mode . tide-mode)
  :config
  (tide-setup)
  (flycheck-mode)
  (setq flycheck-check-syntax-automatically '(save mode-enabled))
  (setq company-tooltip-align-annotations t)
  (eldoc-mode)
  (tide-hl-identifier-mode)
  (company-mode))
```


## Yaml

```emacs-lisp
(use-package yaml-mode
  :straight t
  :mode ("\\.yml\\'" . yaml-mode))
```


## Docker

```emacs-lisp
(use-package docker
  :straight t
  :commands docker)

(use-package dockerfile-mode
  :straight t
  :mode ("Dockerfile\\'" . dockerfile-mode))
```


## Yasnippet


### Config

```emacs-lisp
(use-package yasnippet
  :straight t
  :hook ((lisp-interaction-mode . yas-minor-mode)
	 (emacs-lisp-mode . yas-minor-mode)
	 (lisp-mode . yas-minor-mode)
	 (org-mode . yas-minor-mode)
	 (c++-mode . yas-minor-mode)
	 (c-mode . yas-minor-mode)))

(use-package yasnippet-snippets
  :straight t
  :after yasnippet
  :defer 3.2
  :config (yas-reload-all))
```


### Tiny

Tiny Is Not Yasnippet

```emacs-lisp
(use-package tiny
  :straight t
  :commands tiny-expand)
```


# Dashboard

```emacs-lisp
(use-package dashboard
  :straight t
  :init
  (setq initial-buffer-choice (lambda () (get-buffer "*dashboard*")))
  :config
  ;; Dashboard requirements.
  (use-package page-break-lines
    :straight t)
  (use-package all-the-icons
    :straight t)
  ;; Dashboard configuration.
  (dashboard-setup-startup-hook)
  (setq dashboard-banner-logo-title "Welcome to Emacs")
  (setq dashboard-startup-banner 'logo)
  (setq dashboard-items '((recents . 5)
			  (agenda . 5)))
  (setq dashboard-set-init-info t)
  (setq dashboard-set-heading-icons t)
  (setq dashboard-set-file-icons t))
```


# Org


## Config

```emacs-lisp
(use-package org
  :straight t
  :mode ("\\.org\\'" . org-mode)
  :diminish org-indent-mode
  :config
  (setq org-agenda-files (list "~/notes/agenda.org"))
  (require 'ox-html)
  (require 'ox-latex)
  (require 'ox-md)
  (setq org-startup-indented t)
  (org-babel-do-load-languages
   'org-babel-load-languages
   '((python . t)
     (emacs-lisp . t)
     (shell . t)
     (lisp . t)
     (C . t)))
  (setq org-src-window-setup 'current-window)
  (setq org-agenda-window-setup 'current-window))

(use-package org-bullets
  :after org
  :straight t
  :hook (org-mode . org-bullets-mode))

(use-package ob-async
  :straight t
  :defer 4.3)

;; references
(use-package org-ref
  :straight t
  :commands org-ref-ivy-insert-cite-link
  :config
  (setq reftex-default-bibliography '("~/notes/roam/math.bib")
	org-ref-default-bibliography '("~/notes/roam/math.bib")))
```


## Roam

```emacs-lisp
(use-package org-roam
  :straight t
  ;; :ensure-system-package
  ;; ((sqlite3)
  ;;  (graphviz))
  :commands org-roam-mode
  :bind
  (:map org-roam-mode-map
	(("C-c n l" . org-roam)
	 ("C-c n f" . org-roam-find-file)
	 ("C-c n g" . org-roam-graph-show))
	:map org-mode-map
	(("C-c n i" . org-roam-insert))
	(("C-c n I" . org-roam-insert-immediate)))
  :config
  (setq org-roam-directory "~/notes/roam/")
  (setq org-roam-index-file "Index.org")
  (setq org-roam-graph-node-extra-config '(("shape" . "ellipse")
					   ("style" . "rounded,filled")
					   ("fillcolor" . "#EFEFFF")
					   ("color" . "#DEDEFF")
					   ("fontcolor" . "#111111")))
  (setq org-roam-graph-viewer "brave-browser")
  (setq org-roam-capture-templates
	(list `("d" "default" plain #'org-roam--capture-get-point
		"%?"
		:file-name "%<%Y%m%d%H%M%S>-${slug}"
		:head ,(concat "#+title: ${title}\n"
			       "#+author: \"Caio Henrique\"\n"
			       "#+date: <%<%Y-%m-%d>>\n")
		:unnarrowed t)))
  (require 'org-roam-protocol))

(use-package org-roam-server
  :straight t
  :commands org-roam-server-mode
  :config
  (setq org-roam-server-host "0.0.0.0"
	org-roam-server-port 8082
	org-roam-server-export-inline-images t
	org-roam-server-authenticate nil
	org-roam-server-network-poll t
	org-roam-server-network-arrows nil
	org-roam-server-network-label-truncate t
	org-roam-server-network-label-truncate-length 60
	org-roam-server-network-label-wrap-length 20))
```


## Exporting

```emacs-lisp
;; Export to html with syntax highlighting
(use-package htmlize
  :after org
  :straight t
  :commands org-export-dispatch)

;; Export to Markdown with syntax highlighting
(use-package ox-gfm
  :after org
  :straight t
  :commands org-gfm-export-to-markdown)
```


### Presentations

1.  Requirements

    Requires reveal.js to create html presentations.

2.  Config

    ```emacs-lisp
    ;; Package used to create presentations using reveal.js.
    ;; Requires the installation of reveaj.js.
    (use-package ox-reveal
      :after org
      :straight t
      :commands org-reveal-export-to-html
      :config
      (setq org-reveal-root "file:///home/spvk/notes/presentations/reveal.js"))
    ```


# LaTeX

```emacs-lisp
(use-package tex
  :straight auctex
  :hook (TeX-mode . (lambda ()
		      (flycheck-mode)
		      (company-mode)))
  :config
  (setq TeX-auto-save t)
  (setq TeX-parse-self t))

(use-package company-auctex
  :straight t
  :after (auctex company)
  :config
  (company-auctex-init))

(use-package ivy-bibtex
  :straight t
  :after auctex
  :config
  (setq bibtex-completion-bibliography
	'("~/projects/tex/test.bib")))
```


# Theme

My favorite themes packages are zerodark-theme, kaolin-themes, moe-theme and dracula-theme. There is a function bound to <f5> to switch between light mode and dark mode.

```emacs-lisp
(use-package kaolin-themes
  :straight t)

(use-package doom-themes
  :straight t)

(defun toggle-light-dark-theme--custom-choices (theme)
  "Used to create the choice widget options of the
toggle-light-dark-theme custom variables."
  `(const :tag ,(symbol-name theme) ,theme))

(defcustom toggle-light-dark-theme-light-theme 'doom-acario-light
  "The light theme that the function toggle-light-dark-theme will use."
  :type `(choice ,@(mapcar #'toggle-light-dark-theme--custom-choices
			   (custom-available-themes))))

(defcustom toggle-light-dark-theme-dark-theme 'kaolin-galaxy
  "The dark theme that the function toggle-light-dark-theme will use."
  :type `(choice ,@(mapcar #'toggle-light-dark-theme--custom-choices
			   (custom-available-themes))))

(defvar toggle-light-dark-theme--current-theme 'light)

(defun toggle-light-dark-theme ()
  "Disables all custom enabled themes and then toggles between a
light and a dark theme, which are the values of the variables
toggle-light-dark-theme-light-theme and toggle-light-dark-theme-dark-theme."
  (interactive)
  (mapc #'disable-theme custom-enabled-themes)
  (powerline-reset)
  (cond ((eq toggle-light-dark-theme--current-theme 'light)
	 (load-theme toggle-light-dark-theme-dark-theme)
	 (setq toggle-light-dark-theme--current-theme 'dark))
	(t
	 (load-theme toggle-light-dark-theme-light-theme)
	 (setq toggle-light-dark-theme--current-theme 'light))))

(define-key-after
  global-map
  [menu-bar options customize customize-toggle-light-dark-theme]
  '("Toggle light and dark theme" . toggle-light-dark-theme)
  'customize-themes)

(global-set-key (kbd "<f5>") #'toggle-light-dark-theme)
```


# Global (better UX)


## Hydra

```emacs-lisp
(use-package hydra
  :straight t
  :defer 2.5)
```


### Hydra Modal editing

![img](./imgs/hydra.png)

```emacs-lisp
(load-file (concat user-emacs-directory "other-settings/hydra-modal.el"))
```


### Hydra EMMS

```emacs-lisp
(defhydra hydra-emms (:color teal
			     :hint nil)
  "
    _p_:laylist  _b_:rowse  _r_:eset  _c_:onnect
    _k_:ill      _u_:pdate
  "
  ("q" nil "quit")
  ("p" emms)
  ("b" emms-smart-browse)
  ("r" emms-player-mpd-update-all-reset-cache)
  ("c" mpd/start-music-daemon)
  ("k" mpd/kill-music-daemon)
  ("u" mpd/update-database))

(global-set-key (kbd "s-m") 'hydra-emms/body)
(global-set-key (kbd "C-: m") 'hydra-emms/body)
```


### Hydra to launch programs

```emacs-lisp
(defun exwm-async-run (name)
  (start-process name nil name))

(defhydra hydra-exwm (:color teal
			     :hint nil)
  "
  _b_:rave  _a_:lacritty  _e_:lfeed  _p_:ass  _y_:tdl
  _g_:nus   _d_:ebbugs    _s_:hell
  "
  ("q" nil "quit")
  ("<f4>" nil "quit")
  ("b" (exwm-async-run "brave-browser"))
  ("a" (exwm-async-run "alacritty"))
  ("e" (elfeed))
  ("p" (pass))
  ("g" (gnus))
  ("d" debbugs-gnu)
  ("s" eshell)
  ("y" (hydra-ytdl/body)))
(global-set-key (kbd "<f4>") 'hydra-exwm/body)
```


### Hydra MC

```emacs-lisp
(defhydra hydra-multiple-cursors (:color teal
					 :hint nil
					 :post hydra-movement/cond-body-call)
  "
    _e_:dit lines   _a_:ll like this  _l_:etters  _n_:umbers
  "
  ("q" nil "quit")
  ("e" mc/edit-lines)
  ("l" mc/insert-letters)
  ("n" mc/insert-numbers)
  ("a" mc/mark-all-like-this))

(global-set-key (kbd "C-: c") 'hydra-multiple-cursors/body)
```


### Hydra AVY

```emacs-lisp
(defhydra hydra-avy (:color teal
			    :hint nil
			    :post hydra-movement/cond-body-call)
  "
  _s_: word 1   _n_: word bellow   _p_: word above
  _l_: line     _c_: char timer    _g_: char timer
  "
  ("q" nil "quit")
  ("s" avy-goto-word-1)
  ("p" avy-goto-word-1-above)
  ("n" avy-goto-word-1-below)
  ("l" avy-goto-line)
  ("c" avy-goto-char-timer)
  ("g" avy-goto-char-timer))

(global-set-key (kbd "M-s s") 'hydra-avy/body)
```


### Hydra Youtube dl

```emacs-lisp
(defhydra hydra-ytdl (:color teal
			     :hint nil)
  "
  _d_:ownload   _l_:ist  _o_:pen  _p_:laylist
  "
  ("q" nil "quit")
  ("d" ytdl-download)
  ("o" ytdl-download-open)
  ("l" ytdl-show-list)
  ("p" ytdl-download-playlist))
```


### Hydra Eyebrowse

```emacs-lisp
(defhydra hydra-eyebrowse (:color amaranth :hint nil)
  "
%s(eyebrowse-mode-line-indicator)  
_p_: prev wind   _c_: creat wind  _r_: renam wind
_n_: next wind   _C_: close wind  _l_: last wind
_0_: switch to 0      ^^...       _9_: switch to 9   
  "
  ("q" nil "quit")
  ("<f2>" nil "quit")
  ("p" eyebrowse-prev-window-config nil)
  ("n" eyebrowse-next-window-config nil)
  ("l" eyebrowse-last-window-config nil)
  ("r" eyebrowse-rename-window-config nil)
  ("c" eyebrowse-create-window-config nil)
  ("C" eyebrowse-close-window-config nil)
  ("0" eyebrowse-switch-to-window-config-0 nil)
  ("1" eyebrowse-switch-to-window-config-1 nil)
  ("2" eyebrowse-switch-to-window-config-2 nil)
  ("3" eyebrowse-switch-to-window-config-3 nil)
  ("4" eyebrowse-switch-to-window-config-4 nil)
  ("5" eyebrowse-switch-to-window-config-5 nil)
  ("6" eyebrowse-switch-to-window-config-6 nil)
  ("7" eyebrowse-switch-to-window-config-7 nil)
  ("8" eyebrowse-switch-to-window-config-8 nil)
  ("9" eyebrowse-switch-to-window-config-9 nil))

(global-set-key (kbd "<f2>") 'hydra-eyebrowse/body)
```


### Hydra dump jump

```emacs-lisp
(defhydra hydra-dumb-jump (:color teal :columns 3)
  "Dumb Jump"
  ("q" nil "quit")
  ("<f3>" nil "quit")
  ("j" dumb-jump-go "Go")
  ("o" dumb-jump-go-other-window "Other window")
  ("e" dumb-jump-go-prefer-external "Go external")
  ("x" dumb-jump-go-prefer-external-other-window "Go external other window")
  ("i" dumb-jump-go-prompt "Prompt")
  ("l" dumb-jump-quick-look "Quick look")
  ("b" dumb-jump-back "Back"))

(global-set-key (kbd "<f3>") 'hydra-dumb-jump/body)
```


### Hydra lsp

```emacs-lisp
(defhydra hydra-lsp (:color teal
			    :hint nil)
  "
  _b_:ack  _j_:ump def  _d_:ecl  _D_:escribe  _h_:ighlight  _H_:ighlight doc
  _l_:ens  _r_:ename _L_: avy lens
  "
  ("q" nil "quit")
  ("b" xref-pop-marker-stack)
  ("j" lsp-find-definition)
  ("d" lsp-find-declaration)
  ("D" lsp-describe-thing-at-point)
  ("H" lsp-document-highlight)
  ("h" lsp-toggle-symbol-highlight)
  ("l" lsp-lens-mode)
  ("r" lsp-rename)
  ("L" lsp-avy-lens)
  ("f" lsp-format-region))

(global-set-key (kbd "<f6>") 'hydra-lsp/body)
```


### Hydra Org Roam

```emacs-lisp
(defhydra hydra-roam (:color teal
			     :hint nil)
  "
  _f_:ind file  _i_:nsert  _I_:ndex  _g_:raph
  _c_:apture  _s_:erver
  "
  ("q" nil "quit")
  ("f" org-roam-find-file)
  ("i" org-roam-insert)
  ("I" org-roam-jump-to-index)
  ("g" org-roam-graph)
  ("c" org-roam-capture)
  ("s" org-roam-server-mode))

(global-set-key (kbd "C-: r") 'hydra-roam/body)
```


### Hydra frames

```emacs-lisp
(defhydra hydra-frames-windows (:color teal
				       :hint nil)
  "
  m: make-frame   d: delete-frame          Z: suspend-frame
  q: quit         b: buffer-other-frame    M: toggle-maximize
  o: other-frame  f: find-file-other-frame
  "
  ("m" make-frame-command)
  ("b" switch-to-buffer-other-frame)
  ("d" delete-frame)
  ("o" other-frame)
  ("f" find-file-other-frame)
  ("Z" suspend-frame)
  ("M" toggle-frame-maximized)
  ("q" nil))

(global-set-key (kbd "C-z") 'hydra-frames-windows/body)
```


## Ivy

```emacs-lisp
;;; Global
;; Ivy is a generic completion tool
(use-package ivy
  :straight t
  :diminish ivy-mode
  :defer 0.9
  :config
  (ivy-mode))

(use-package swiper
  :straight t
  :after ivy
  :bind (("C-s" . swiper)
	 ("C-M-s" . swiper-thing-at-point)))

(use-package counsel
  :straight t
  :after ivy
  :diminish counsel-mode
  :config
  (counsel-mode))

(use-package ivy-avy
  :straight t
  :after (ivy avy))
```


## Regular expressions

```emacs-lisp
(use-package visual-regexp-steroids
  :straight t
  :commands vr/replace)
```


## Kill ring

```emacs-lisp
(use-package popup-kill-ring
  :straight t
  :bind (("M-y" . popup-kill-ring)))
```


## Which-key

```emacs-lisp
(use-package which-key
  :straight t
  :commands which-key-mode)
```


## Modeline

```emacs-lisp
(display-time-mode t)

(use-package spaceline
  :straight t
  :defer 2.2
  :config
  (require 'spaceline-config)
  (setq powerline-default-separator 'arrow)
  (setq spaceline-line-column-p nil)
  (setq spaceline-buffer-size nil)
  (setq spaceline-workspace-numbers-unicode t)
  (setq spaceline-buffer-encoding-abbrev-p nil)
  (spaceline-spacemacs-theme))
```


## Parentheses

```emacs-lisp
(use-package smartparens
  :straight t
  :defer 5.1
  :diminish smartparens-mode
  :config
  (smartparens-global-mode))

(use-package highlight-parentheses
  :straight t
  :defer 5.3
  :diminish highlight-parentheses-mode
  :config (global-highlight-parentheses-mode))

(defvar show-paren-delay 0)
(show-paren-mode t)
```


## Buffer moving

```emacs-lisp
(use-package buffer-move
  :straight t
  :bind
  (("C-c <C-up>" . buf-move-up)
   ("C-c <C-down>" . buf-move-down)
   ("C-c <C-left>" . buf-move-left)
   ("C-c <C-right>" . buf-move-right)))
```


## Windows moving

```emacs-lisp
(global-set-key (kbd "C-c <M-up>") 'windmove-up) 
(global-set-key (kbd "C-c <M-down>") 'windmove-down) 
(global-set-key (kbd "C-c <M-right>") 'windmove-right) 
(global-set-key (kbd "C-c <M-left>") 'windmove-left) 

(use-package ace-window
  :straight t
  :commands ace-window)
```


## Multiple cursors

```emacs-lisp
(use-package multiple-cursors
  :straight t
  :bind (("C-: C-m b" . mc/edit-lines)
	 ("C-: C-m a" . mc/mark-all-like-this)
	 ("C-: C-m >" . mc/mark-next-like-this)
	 ("C-: C-m <" . mc/mark-previous-like-this)))
```


## Avy

```emacs-lisp
(use-package avy
  :straight t
  :commands hydra-avy/body)
```


## Undo-tree

```emacs-lisp
(use-package undo-tree
  :straight t
  :defer 3.1
  :diminish undo-tree-mode
  :config (global-undo-tree-mode))
```


## Dired

```emacs-lisp
(use-package dired
  :hook (dired-mode . dired-omit-mode)
  :bind (:map dired-mode-map
	      ("<return>" . dired-find-alternate-file)
	      ("<dead-circumflex>" . dired-up-directory)
	      ("E" . image-dired)
	      ("J" . dired-omit-mode)))

(use-package dired-x
  :config
  (setq dired-omit-verbose nil)
  (setq dired-omit-files
	"^\\..+$"))

(use-package peep-dired
  :straight t
  :bind (:map dired-mode-map
	      ("P" . 'peep-dired)))

(use-package dired-rainbow
  :straight t
  :defer 3.2
  :config
  (dired-rainbow-define-chmod directory "#6cb2eb" "d.*")
  (dired-rainbow-define html "#eb5286" ("css" "less" "sass" "scss" "htm" "html"
					"jhtm" "mht" "eml" "mustache" "xhtml"))
  (dired-rainbow-define xml "#f2d024" ("xml" "xsd" "xsl" "xslt" "wsdl" "bib"
				       "json" "msg" "pgn" "rss" "yaml" "yml" "rdata"))
  (dired-rainbow-define document "#9561e2" ("docm" "doc" "docx" "odb" "odt"
					    "pdb" "pdf" "ps" "rtf" "djvu" "epub"
					    "odp" "ppt" "pptx"))
  (dired-rainbow-define markdown "#ffed4a" ("org" "etx" "info" "markdown"
					    "md" "mkd" "nfo" "pod" "rst"
					    "tex" "textfile" "txt"))
  (dired-rainbow-define database "#6574cd" ("xlsx" "xls" "csv" "accdb" "db" "mdb"
					    "sqlite" "nc"))
  (dired-rainbow-define media "#de751f" ("mp3" "mp4" "MP3" "MP4" "avi" "mpeg" "mpg"
					 "flv" "ogg" "mov" "mid" "midi" "wav" "aiff" "flac"))
  (dired-rainbow-define image "#f66d9b" ("tiff" "tif" "cdr" "gif" "ico"
					 "jpeg" "jpg" "png" "psd" "eps" "svg"))
  (dired-rainbow-define log "#c17d11" ("log"))
  (dired-rainbow-define shell "#f6993f" ("awk" "bash" "bat" "sed" "sh" "zsh" "vim"))
  (dired-rainbow-define interpreted "#38c172" ("py" "ipynb" "rb" "pl" "t" "msql" "mysql"
					       "pgsql" "sql" "r" "clj" "cljs" "scala" "js"))
  (dired-rainbow-define compiled "#4dc0b5" ("asm" "cl" "lisp" "el" "c" "h" "c++"
					    "h++" "hpp" "hxx" "m" "cc" "cs" "cp" "cpp"
					    "go" "f" "for" "ftn" "f90" "f95" "f03" "f08"
					    "s" "rs" "hi" "hs" "pyc" ".java"))
  (dired-rainbow-define executable "#8cc4ff" ("exe" "msi"))
  (dired-rainbow-define compressed "#51d88a" ("7z" "zip" "bz2" "tgz" "txz" "gz"
					      "xz" "z" "Z" "jar" "war" "ear" "rar"
					      "sar" "xpi" "apk" "xz" "tar"))
  (dired-rainbow-define packaged "#faad63" ("deb" "rpm" "apk" "jad" "jar" "cab" "pak"
					    "pk3" "vdf" "vpk" "bsp"))
  (dired-rainbow-define encrypted "#ffed4a" ("gpg" "pgp" "asc" "bfe" "enc" "signature"
					     "sig" "p12" "pem"))
  (dired-rainbow-define fonts "#6cb2eb" ("afm" "fon" "fnt" "pfb" "pfm" "ttf" "otf"))
  (dired-rainbow-define partition "#e3342f" ("dmg" "iso" "bin" "nrg" "qcow" "toast"
					     "vcd" "vmdk" "bak"))
  (dired-rainbow-define vc "#0074d9" ("git" "gitignore" "gitattributes" "gitmodules"))
  (dired-rainbow-define-chmod executable-unix "#38c172" "-.*x.*"))
```


## Windows management

```emacs-lisp
(use-package eyebrowse
  :straight t
  :defer 3.4
  :config (eyebrowse-mode t))
```


## Smart region expanding

```emacs-lisp
(use-package expand-region
  :straight t
  :bind (("C-=" . er/expand-region)))
```


## Tool bar, menu bar, line numbering etc

```emacs-lisp
;;; Variables
(global-visual-line-mode)
(global-set-key (kbd "TAB") 'self-insert-command)
(global-set-key (kbd "\C-c h") 'highlight-symbol-at-point)
(setq visible-bell 1)

;; For versions >= 27, this is done on early-init.el
(when (< emacs-major-version 27)
  (menu-bar-mode -1)
  (tool-bar-mode -1)
  (scroll-bar-mode -1))
```


## Change backup/autosave folder

```emacs-lisp
;;; Change the backup/autosave folder.
(defvar backup-dir (expand-file-name (concat user-emacs-directory "backup/")))
(defvar autosave-dir (expand-file-name (concat user-emacs-directory "autosave/")))
(setq backup-directory-alist (list (cons ".*" backup-dir)))
(setq auto-save-list-file-prefix autosave-dir)
(setq auto-save-file-name-transforms `((".*" ,autosave-dir t)))
```


## Sticky buffers

<https://lists.gnu.org/archive/html/help-gnu-emacs/2007-05/msg00975.html>

```emacs-lisp
(define-minor-mode sticky-buffer-mode
  "Make the current window always display this buffer."
  nil " sticky" nil
  (set-window-dedicated-p (selected-window) sticky-buffer-mode))
```


## Read process output

Sets read-process-output-max to 1mb since the default is low. This should improve things that use servers like LSP.

```emacs-lisp
(setq read-process-output-max (* 1024 1024))
```


## Spell checking

I use aspell for spell checking.


### Config

```emacs-lisp
(defvar ispell-program-name "aspell")
```


## Diminish


### Config

```emacs-lisp
(diminish 'visual-line-mode)
(diminish 'auto-revert-mode)
(diminish 'eldoc-mode)
```


## Treemacs

```emacs-lisp
(use-package treemacs
  :straight t
  :commands treemacs)
```


## Display commands

```emacs-lisp
(use-package command-log-mode
  :straight t
  :commands global-command-log-mode)
```


# Programs


## Password manager

Requires pass.

```emacs-lisp
(use-package pass
  :straight t
  :commands pass)
```


## Debbugs

```emacs-lisp
(use-package debbugs
  :straight t
  :commands debbugs-gnu)
```


## PDF


### Requirements

See <https://github.com/politza/pdf-tools>.


### Config

```emacs-lisp
(use-package pdf-tools
  :straight t
  :mode ("\\.pdf\\'" . pdf-view-mode))
```


## EMMS

I use mpd/mpc to play music. I used Uncle Dave's config as a reference for the following settings.

```emacs-lisp
(use-package emms
  :straight t
  :config
  (require 'emms-setup)
  (require 'emms-player-mpd)
  (emms-all)
  (setq emms-seek-seconds 5)
  (setq emms-player-list '(emms-player-mpd emms-player-mpv))
  (setq emms-info-functions '(emms-info-mpd))
  (setq emms-player-mpd-server-name "localhost")
  (setq emms-player-mpd-server-port "6601")
  :commands hydra-emms/body
  :bind
  (("<XF86AudioPrev>" . emms-previous)
   ("<XF86AudioNext>" . emms-next)
   ("<XF86AudioPlay>" . emms-pause)
   ("<XF86AudioStop>" . emms-stop)))

(setq mpc-host "localhost:6601")

(defun mpd/start-music-daemon ()
  "Start MPD, connects to it and syncs the metadata cache."
  (interactive)
  (shell-command "mpd")
  (mpd/update-database)
  (emms-player-mpd-connect)
  (emms-cache-set-from-mpd-all)
  (message "MPD Started!"))

(defun mpd/kill-music-daemon ()
  "Stops playback and kill the music daemon."
  (interactive)
  (emms-stop)
  (call-process "killall" nil nil nil "mpd")
  (message "MPD Killed!"))


(defun mpd/update-database ()
  "Updates the MPD database synchronously."
  (interactive)
  (call-process "mpc" nil nil nil "update")
  (message "MPD Database Updated!"))
```


## RSS


### Requirements

cURL.


### Config

```emacs-lisp
(use-package elfeed
  :straight t
  :commands elfeed
  :config (load-file (concat user-emacs-directory "personal-settings/feeds.el")))
```


## Youtube-dl

```emacs-lisp
(use-package ytdl
  :straight t
  :commands ytdl-download
  :config
  (setq ytdl-media-player 'mpv))
```


## EXWM

```emacs-lisp
(use-package exwm
  :straight t
  :bind (("<XF86AudioRaiseVolume>" . (lambda ()
				       (interactive)
				       (call-process-shell-command "amixer set Master 5%+" nil 0)))
	 ("<XF86AudioLowerVolume>" . (lambda ()
				       (interactive)
				       (call-process-shell-command "amixer set Master 5%-" nil 0)))
	 ("<XF86AudioMute>" . (lambda ()
				(interactive)
				(call-process-shell-command "amixer set Master toggle" nil 0)))
	 ("<print>" . (lambda ()
			(interactive)
			(call-process-shell-command "flameshot gui" nil 0))))
  :config
  (add-hook 'exwm-init-hook (lambda ()
			      (exwm-input-set-simulation-keys
			       '(([?\C-w] . ?\C-x)
				 ([?\M-w] . ?\C-c)
				 ([?\C-y] . ?\C-v)
				 ([?\C-s] . ?\C-f)))))

  (setq exwm-replace nil)

  (require 'exwm-config)

  (setq exwm-workspace-number 4)

  (require 'exwm-randr)

  (exwm-enable)

  (setq exwm-randr-workspace-output-plist '(0 "eDP-1" 1 "HDMI-1"))

  (add-hook 'exwm-randr-screen-change-hook
	    (lambda ()
	      (start-process-shell-command
	       "xrandr" nil "xrandr --output eDP-1 --right-of HDMI-1 --auto")))
  (exwm-randr-enable))
```


## Screenshots

Requires Emacs built with cairo.

```emacs-lisp
(defun screenshot-svg ()
  "Save a screenshot of the current frame as an SVG image.
Saves to a temp file and puts the filename in the kill ring."
  (interactive)
  (let* ((filename (make-temp-file "Emacs" nil ".svg"))
	 (data (x-export-frames nil 'svg)))
    (with-temp-file filename
      (insert data))
    (kill-new filename)
    (message filename)))
```


# Blog

```emacs-lisp
(use-package org-static-blog
  :straight t
  :commands (org-static-blog-create-new-post
	     org-static-blog-publish
	     org-static-blog-publish-file)
  :config (load-file (concat user-emacs-directory "personal-settings/blog.el")))
```


# Webjump

```emacs-lisp
(use-package webjump
  :commands webjump
  :config (load-file (concat user-emacs-directory "personal-settings/webjump-sites.el")))
```


# Gnus

```emacs-lisp
(use-package gnus
  :commands gnus
  :config (setq gnus-init-file (concat user-emacs-directory "personal-settings/gnus.el")))
```
