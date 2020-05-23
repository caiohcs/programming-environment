- [Programming](#orgbc4fb26)
  - [C and C++](#org11920d7)
    - [Requirements](#org00dc6e8)
    - [Config](#orgdc400c4)
  - [Golang](#org1869dd2)
    - [Requirements](#org4871c2e)
    - [Config](#org4eef6dd)
  - [Color identifiers](#org269d0c5)
  - [Lisp](#org9fc038c)
    - [Config](#orgcce1903)
- [Spell checking](#orgc678fa3)
  - [Config](#orga337660)
- [Dashboard](#org439b377)
- [Org](#org41ee518)
  - [Config](#org9c81844)
  - [Exporting](#orgc6e904a)
  - [Presentations](#org68c980b)
    - [Requirements](#org08562e5)
    - [Config](#orgdfd244b)
- [Company](#org0084be5)
- [Magit](#org2faee1a)
- [Theme](#org3cac572)
- [Treemacs](#org97e28f4)
- [Docker](#orgc033046)
- [Yaml](#org00b1a98)
- [Global](#orgdc3090a)
  - [Emacs completion](#org54ef3d6)
  - [Parentheses](#org14a3a57)
  - [Moving buffers](#orgacf697f)
  - [Multiple cursors](#org4e8b029)
  - [Text navigation](#orga4ce670)
  - [Windows managing](#orgd2e66f7)
  - [Smart region expanding](#orgc1ed7cc)
  - [Syntax checking](#orgb1b61b6)
  - [Tool bar, menu bar, line numbering etc](#orgc66c6b7)
  - [Change backup/autosave folder](#org5a956dc)
- [Latin accents](#orgecd520c)

![img](./imgs/my-emacs.png)


<a id="orgbc4fb26"></a>

# Programming


<a id="org11920d7"></a>

## C and C++


<a id="org00dc6e8"></a>

### Requirements

Most of the C and C++ utilities requires clang installed on the system.


<a id="orgdc400c4"></a>

### Config

```emacs-lisp
;;; C/C++
(use-package cmake-ide
  :ensure t
  :defer 7.4
  :config (cmake-ide-setup))

(defun my-c-mode-common-hook ()
  (c-set-offset 'substatement-open 0)
  (setq  c-default-style "bsd"
	 c-basic-offset 4
	 c-indent-level 4
	 c-indent-tabs-mode t
	 c-tab-always-indent t
	 c++-tab-always-indent t
	 tab-width 4
	 backward-delete-function nil)
  (aggressive-indent-mode)
  (company-mode))

(add-hook 'c++-mode-common-hook 'my-c-mode-common-hook)
(add-hook 'c-mode-common-hook 'my-c-mode-common-hook)
```


<a id="org1869dd2"></a>

## Golang


<a id="org4871c2e"></a>

### Requirements

Autocompletion requires gocode, available at <https://github.com/nsf/gocode>. We can install gocode with go get, then we need to create a symbolic link at /usr/bin.


<a id="org4eef6dd"></a>

### Config

```emacs-lisp
;; Requires gocode https://github.com/nsf/gocode
;; After installing gocode, we also have to create a symbolic link
(use-package go-mode
  :ensure t
  :mode ("\\.go\\'" . go-mode)
  :config
  (use-package company-go
    :requires company
    :ensure t)
  (use-package go-errcheck
    :ensure t)
  (defun my-go-mode-hook ()
    (setq tab-width 4)
    (setq gofmt-command "goimports")
    (set (make-local-variable 'company-backends) '(company-go))
    (company-mode))
  (add-hook 'go-mode-hook 'my-go-mode-hook))
```


<a id="org269d0c5"></a>

## Color identifiers

```emacs-lisp
(use-package color-identifiers-mode
  :disabled
  :ensure t
  :defer 9)
```


<a id="org9fc038c"></a>

## Lisp

I use Steel Bank Common Lisp.


<a id="orgcce1903"></a>

### Config

```emacs-lisp
;;; Lisp
(use-package slime
  :ensure t
  :config
  (setq inferior-lisp-program "sbcl")
  (setq slime-contribs '(slime-fancy)))

(use-package slime-company
  :after company
  :ensure t
  :init
  (slime-setup '(slime-fancy slime-company)))
```


<a id="orgc678fa3"></a>

# Spell checking

I use aspell for spell checking.


<a id="orga337660"></a>

## Config

```emacs-lisp
(defvar ispell-program-name "aspell")
```


<a id="org439b377"></a>

# Dashboard

```emacs-lisp
(use-package dashboard
  :ensure t
  :init
  (setq initial-buffer-choice (lambda () (get-buffer "*dashboard*")))
  :config
  ;; Dashboard requirements.
  (use-package page-break-lines
    :ensure t)
  (use-package all-the-icons
    :ensure t)
  ;; Dashboard configuration.
  (dashboard-setup-startup-hook)
  (setq dashboard-banner-logo-title "Welcome to Emacs")
  (setq dashboard-startup-banner 'logo)
  (setq dashboard-items '((recents   . 5)
			  (bookmarks . 5)
			  (agenda    . 5)
			  (registers . 5)))
  (setq dashboard-set-init-info t)
  (setq dashboard-set-heading-icons t)
  (setq dashboard-set-file-icons t))
```


<a id="org41ee518"></a>

# Org


<a id="org9c81844"></a>

## Config

```emacs-lisp
;;; org
(use-package org
  :ensure t
  :mode ("\\.org\\'" . org-mode)
  :config
  (setq org-startup-indented t)
  (org-babel-do-load-languages
   'org-babel-load-languages
   '( (python . t)
      (emacs-lisp . t)
      (lisp . t)
      (C . t))))

(use-package org-bullets
  :after org
  :ensure t
  :config (add-hook 'org-mode-hook (lambda () (org-bullets-mode))))
```


<a id="orgc6e904a"></a>

## Exporting

```emacs-lisp
;; Export to html with syntax highlighting
(use-package htmlize
  :after org
  :ensure t)

;; Export to Markdown with syntax highlighting
(use-package ox-gfm
  :after org
  :ensure t)
```


<a id="org68c980b"></a>

## Presentations


<a id="org08562e5"></a>

### Requirements

Requires reveal.js to create html presentations.


<a id="orgdfd244b"></a>

### Config

```emacs-lisp
;; Package used to create presentations using reveal.js.
;; Requires the installation of reveaj.js.
(use-package ox-reveal
  :after org
  :ensure t
  :config
  (setq org-reveal-root "file:///home/spvk/notes/presentations/reveal.js"))
```


<a id="org0084be5"></a>

# Company

```emacs-lisp
(use-package company
  :ensure t
  :defer 2.3
  :init
  (use-package company-quickhelp
    :ensure t)
  :config
  (company-quickhelp-mode))
```


<a id="org2faee1a"></a>

# Magit

```emacs-lisp
(use-package magit
  :ensure t
  :defer 9.2)

(global-set-key (kbd "\C-x g") 'magit-status)
```


<a id="org3cac572"></a>

# Theme

My favorite themes packages are zerodark-theme, kaolin-themes, moe-theme and dracula-theme.

```emacs-lisp
;; zerodark-theme kaolin-themes moe-theme dracula-theme are nice themes
(use-package kaolin-themes
  :ensure t)
```


<a id="org97e28f4"></a>

# Treemacs

```emacs-lisp
(use-package treemacs
  :ensure t
  :defer 12.4)
```


<a id="orgc033046"></a>

# Docker

```emacs-lisp
(use-package docker
  :disabled
  :ensure t
  :defer 30)

(use-package dockerfile-mode
  :disabled
  :ensure t
  :defer 9)
(add-to-list 'auto-mode-alist '("Dockerfile\\'" . dockerfile-mode))
```


<a id="org00b1a98"></a>

# Yaml

```emacs-lisp
(use-package yaml-mode
  :disabled
  :ensure t
  :mode ("\\.yml\\'" . yaml-mode))
```


<a id="orgdc3090a"></a>

# Global


<a id="org54ef3d6"></a>

## Emacs completion

```emacs-lisp
;;; Global
;; Ivy is a generic completion tool
(use-package ivy
  :ensure t
  :config
  (ivy-mode)
  (use-package swiper
    :ensure t
    :bind (("\C-s" . swiper)))
  :config
  (use-package counsel
    :ensure t))
```


<a id="org14a3a57"></a>

## Parentheses

```emacs-lisp
(use-package smartparens
  :ensure t
  :defer 5.1
  :config (smartparens-global-mode))

(use-package highlight-parentheses
  :ensure t
  :defer 12.1
  :config (global-highlight-parentheses-mode))

(defvar show-paren-delay 0)

(show-paren-mode 1)
```


<a id="orgacf697f"></a>

## Moving buffers

```emacs-lisp
(use-package buffer-move
  :ensure t
  :bind
  (("C-c <C-up>"   . buf-move-up)
   ("C-c <C-down>"  . buf-move-down)
   ("C-c <C-left>"  . buf-move-left)
   ("C-c <C-right>" . buf-move-right)))
```


<a id="org4e8b029"></a>

## Multiple cursors

```emacs-lisp
(use-package multiple-cursors
  :ensure t
  :bind (("C-: C-m b" . mc/edit-lines)
	 ("C-: C-m a" . mc/mark-all-like-this)
	 ("C-: C-m >" . mc/mark-next-like-this)
	 ("C-: C-m <" . mc/mark-previous-like-this)))
```


<a id="orga4ce670"></a>

## Text navigation

```emacs-lisp
(use-package avy
  :ensure t
  :bind (("M-s" . avy-goto-word-1)))
```


<a id="orgd2e66f7"></a>

## Windows managing

```emacs-lisp
(use-package eyebrowse
  :ensure t)
```


<a id="orgc1ed7cc"></a>

## Smart region expanding

```emacs-lisp
(use-package expand-region
  :ensure t
  :bind (("C-=" . er/expand-region)))
```


<a id="orgb1b61b6"></a>

## Syntax checking

```emacs-lisp
;; Provides some syntax checking
(use-package flycheck
  :ensure t
  :defer 11.3
  :init (global-flycheck-mode))

```


<a id="orgc66c6b7"></a>

## Tool bar, menu bar, line numbering etc

```emacs-lisp
;;; Variables
(global-visual-line-mode)
(menu-bar-mode -1)
(tool-bar-mode -1)
(scroll-bar-mode -1)
(global-linum-mode)
(global-set-key (kbd "TAB") 'self-insert-command)
(global-set-key (kbd "\C-c h") 'highlight-symbol-at-point)
```


<a id="org5a956dc"></a>

## Change backup/autosave folder

```emacs-lisp
;;; Change the backup/autosave folder.
(defvar backup-dir (expand-file-name "~/.emacs.d/backup/"))
(defvar autosave-dir (expand-file-name "~/.emacs.d/autosave/"))
(setq backup-directory-alist (list (cons ".*" backup-dir)))
(setq auto-save-list-file-prefix autosave-dir)
(setq auto-save-file-name-transforms `((".*" ,autosave-dir t)))
```


<a id="orgecd520c"></a>

# Latin accents

I created this function to insert the latin accents that I use the most.

```emacs-lisp
;; latin accents
(defun my-latin-accents-function (start end)
  (interactive "r")
  (defun cmp-and-fixcase (reg cmp out)
    (let ((case-fold-search t))
      (if (string-match-p reg cmp)
	  (let ((case-fold-search nil))
	    (if (string-match-p "\\`[a-z]*\\'" reg)
		(progn (delete-region start end) (insert out))
	      (progn (delete-region start end) (insert (upcase out))))) nil)))
  (if (use-region-p)
      (let ((regionp (buffer-substring start end)))
	(cond ((cmp-and-fixcase regionp "aa" "á"))
	      ((cmp-and-fixcase regionp "ga" "à"))
	      ((cmp-and-fixcase regionp "ta" "ã"))
	      ((cmp-and-fixcase regionp "ae" "é"))
	      ((cmp-and-fixcase regionp "ge" "è"))
	      ((cmp-and-fixcase regionp "te" "ẽ"))
	      ((cmp-and-fixcase regionp "ce" "ê"))
	      ((cmp-and-fixcase regionp "co" "ô"))
	      ((cmp-and-fixcase regionp "to" "õ"))
	      ((cmp-and-fixcase regionp "ai" "í")) 
	      ))))
(global-set-key (kbd "C-: C-a") 'my-latin-accents-function)
```