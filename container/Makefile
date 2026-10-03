# Builds the mrcal container image, and installs host-side wrappers that run the
# mrcal tools inside it:
#
#   make build    # build the image
#   make links    # symlink each tool in $(BINDIR) to mrcal-container
#   make unlinks  # remove those symlinks
#
# BINDIR defaults to ~/.local/bin. MRCAL_CONTAINER_IMAGE and
# MRCAL_CONTAINER_ENGINE pick the image tag and the container engine; the
# wrapper reads the same variables from the environment

MRCAL_CONTAINER_IMAGE  ?= mrcal
MRCAL_CONTAINER_ENGINE ?= podman
BINDIR                 ?= $(HOME)/.local/bin

WRAPPER := $(abspath mrcal-container)

# The tools mrcal ships (DIST_BIN in the top-level Makefile)
TOOLS := $(shell sed -n '/^DIST_BIN *:=/,/^ *$$/{s/DIST_BIN *:=//;s/\\//;p}' ../Makefile)

build:
	$(MRCAL_CONTAINER_ENGINE) build -t $(MRCAL_CONTAINER_IMAGE) -f Containerfile ..

# Only creates links or replaces our own; anything else in the way (a native
# install, another project's wrapper) is left alone and reported
links:
	@mkdir -p $(BINDIR)
	@for t in $(TOOLS); do f=$(BINDIR)/$$t;                                   \
	  if [ -e $$f -o -L $$f ] && [ "$$(readlink $$f)" != $(WRAPPER) ]; then \
	    echo "Skipping $$f: already exists" >&2;                          \
	  else                                                                \
	    ln -sfn $(WRAPPER) $$f || exit 1;                                 \
	  fi;                                                                 \
	done
	@echo "Linked mrcal tools into $(BINDIR)"

# Removes every symlink pointing at our wrapper
unlinks:
	find $(BINDIR) -maxdepth 1 -lname $(WRAPPER) -delete

.PHONY: build links unlinks
