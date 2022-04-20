
# converting github flavored markdown to html5
MARKDOWN := pandoc --from markdown_github+pandoc_title_block --to html5 --standalone 

DEPLOY = build
DEPLOY_DIRECTORY = ./$(DEPLOY)/

find_all = $(shell find ./source -type f -name $(1))

# find all markdown files
ALL_MD = $(call find_all,'*.md')

# For all known markdown files: change md extension to html and prepend the
# deploy directory.
HTML_FROM_MD := $(patsubst %.md,%.html,$(ALL_MD))

# Map a function that takes two arguments
map = $(foreach a,$(3),$(call $(1),$(2),$(a)))

# File types that need to be processed
PROCESS_TYPES = %.md

# File types that control processing
CONTROL_TYPES = %.tmp

### Identity Files

ALL = $(call find_all,'*.*')
# Non-identity types are types we want to process
# anything in the deploy directory
# and files we want to ignore:
EXCLUDE_TYPES = $(PROCESS_TYPES) $(CONTROL_TYPES) $(DEPLOY_DIRECTORY)% ./%.DS_Store

# filter out excluded types; everything else gets transfered as-is... "identity"
ALL_IDENTITY := $(call map,filter-out,$(EXCLUDE_TYPES),$(ALL))

# Everything that needs deploying :
# all the identity files
# and all the html files derived from markdown
ALL_TO_DEPLOY = $(ALL_IDENTITY) $(HTML_FROM_MD)
DEPLOY_TARGETS := $(subst ./,$(DEPLOY_DIRECTORY),$(ALL_TO_DEPLOY))

# First recipe is default. Nothing to do except dependency on all html files.
bake: $(DEPLOY_TARGETS)

clean:
	@rm -rf $(DEPLOY_DIRECTORY)

# Recipe for html files in the deploy directory for a corresponding markdown
# file
$(addprefix $(DEPLOY_DIRECTORY),%.html): %.md 
	@mkdir -p $(dir $@)
	@$(MARKDOWN) $< --output $@

REMOTE = github
BRANCH = gh-pages

deploy: bake
	git add $(DEPLOY)
	git commit -m 'Deploy'
	git subtree push --prefix=$(DEPLOY) $(REMOTE) $(BRANCH)

undeploy:
	git push $(REMOTE) `git subtree split --prefix $(DEPLOY) $(BRANCH)`:$(BRANCH) --force
