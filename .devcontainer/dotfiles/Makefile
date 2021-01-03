### Makefile to Symlink Dotfiles for the vscode user in /home/vscode
SHELL := bash

.PHONY: default
default: dotfiles dotnet-restore

.PHONY: dotfiles
dotfiles: ## Installs the dotfiles.
	# add aliases for dotfiles
	for file in $(shell find $(CURDIR) -name ".*" -not -name ".gitignore" -not -name ".git" -not -name ".config" -not -name ".github" -not -name ".*.swp" -not -name ".gnupg"); do \
		f=$$(basename $$file); \
		ln -sfn $$file $(HOME)/$$f; \
	done; 

	# Gnu Privacy Guard and SSH Keys
	# gpg --list-keys || true;
	# mkdir -p $(HOME)/.gnupg
	# for file in $(shell find $(CURDIR)/.gnupg); do \
	# 	f=$$(basename $$file); \
	# 	ln -sfn $$file $(HOME)/.gnupg/$$f; \
	# done; 
	mkdir -p $(HOME)/.config;
	ln -snf $(CURDIR)/dotnet-tools.json $(HOME)/.config/dotnet-tools.json;

	# git update-index --skip-worktree $(CURDIR)/.gitconfig;
	# cd mkdir -p $(HOME)/.config;
	# mkdir -p $(HOME)/.local/share;
	# ln -snf $(CURDIR)/.fonts $(HOME)/.local/share/fonts;
	ln -snf $(CURDIR)/.bash_profile $(HOME)/.profile;

.PHONY: dotnet-restore
dotnet-restore: ##Restore user tools
	dotnet tool restore

.PHONY: clean
clean: ## Remove only the links in the dotfiles folder 
	cd /home/vscode
	find -type l | while IFS= read -r lnk; do if (readlink "$lnk" | grep -q '^/home/vscode/dotfiles/'); then rm "$lnk"; fi; done

