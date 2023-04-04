DESCRIBE           := $(shell git describe --match "v*" --always --tags)
DESCRIBE_PARTS     := $(subst -, ,$(DESCRIBE))

VERSION_TAG        := $(word 1,$(DESCRIBE_PARTS))
COMMITS_SINCE_TAG  := $(word 2,$(DESCRIBE_PARTS))

VERSION            := $(subst v,,$(VERSION_TAG))
VERSION_PARTS      := $(subst ., ,$(VERSION))

MAJOR              := $(word 1,$(VERSION_PARTS))
MINOR              := $(word 2,$(VERSION_PARTS))
MICRO              := $(word 3,$(VERSION_PARTS))

NEXT_MAJOR         := $(shell echo $$(($(MAJOR)+1)))
NEXT_MINOR         := $(shell echo $$(($(MINOR)+1)))
NEXT_MICRO          = $(shell echo $$(($(MICRO)+1)))

uname_s := $(shell uname -s)
uname_m := $(shell uname -m)
l_uname_s = $(shell echo $(uname_s) | tr A-Z a-z)
l_uname_m = $(shell echo $(uname_m) | tr A-Z a-z)

KERNEL=$(l_uname_s)
ARCH=$(l_uname_m)


# Run acceptance tests
.PHONY: testacc
testacc:
	TF_ACC=1 go test ./... -v $(TESTARGS) -timeout 120m


.PHONY: build info test clean install local-dev-install

default: testacc

all: build

info:
	@echo "Global info"
	@echo "$(KERNEL)"
	@echo "$(ARCH)"		

build:
	@echo " -> Building"
	mkdir -p bin
	CGO_ENABLED=0 go build -trimpath -o bin/terraform-provider-airbyte
	@echo "Built terraform-provider-airbyte"

install: build
	cp bin/terraform-provider-proxmox $$GOPATH/bin/terraform-provider-proxmox

local-dev-install: build
	@echo "Building this release ${GITHUB_USER} on $(KERNEL)/$(ARCH)"
	rm -rf ~/.terraform.d/plugins/localhost/${GITHUB_USER}/airbyte
	mkdir -p ~/.terraform.d/plugins/localhost/${GITHUB_USER}/airbyte/$(MAJOR).$(MINOR).$(NEXT_MICRO)/$(KERNEL)_$(ARCH)/
	cp bin/terraform-provider-airbyte ~/.terraform.d/plugins/localhost/${GITHUB_USER}/airbyte/$(MAJOR).$(MINOR).$(NEXT_MICRO)/$(KERNEL)_$(ARCH)/


clean:
	@git clean -f -d
