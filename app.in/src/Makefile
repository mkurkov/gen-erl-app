## -*- makefile -*-
## Erlang
include ../vsn.mk
.PHONY: all edoc dialyzer clean
DOC_OPTS={dir,\"../edoc\"}

ERL := erl
ERLC := $(ERL)c

INCLUDE_DIRS := ../include ./
INCLUDE_FILES := $(wildcard ../include/*.hrl) $(wildcard ./*.hrl) 

EBIN_DIRS := $(wildcard ../libs/*/ebin)
ERLC_FLAGS := -W $(addprefix -I , $(INCLUDE_DIRS)) $(EBIN_DIRS:%=-pa %)

ifdef TEST
  ERLC_FLAGS += +debug_info -DTEST 
endif

# for unit test
ifdef EUNIT
  ERLC_FLAGS += -DEUNIT
endif

ifdef HIPE
  ERLC_FLAGS += +native +"{hipe, [o3]}"
endif

LOGDIR =./log
CONFDIR =./etc

BASE_DIR := ..
EBIN_DIR := ../ebin
DOC_DIR  = ../edoc
LIBS_DIR = ../libs
EMULATOR := beam

ERL_SOURCES := $(wildcard *.erl)
ERL_MODULES := $(ERL_SOURCES:%.erl=%)
ERL_MODULES_IN_APP := $(ERL_SOURCES:%.erl=`%`,)
ERL_HEADERS := $(wildcard *.hrl) $(wildcard ../include/*.hrl)
ERL_OBJECTS := $(ERL_SOURCES:%.erl=$(EBIN_DIR)/%.$(EMULATOR))
ERL_OBJECTS_LOCAL := $(ERL_SOURCES:%.erl=./%.$(EMULATOR))
APP_TPL_FILES = $(wildcard *.app.src)
APP_FILES = $(APP_TPL_FILES:%.src=%)
CTL_TPL_FILES = $(wildcard *ctl.tpl)
CTL_FILES = $(addprefix $(BASE_DIR)/, $(CTL_TPL_FILES:%.tpl=%))

ALL_FILES = $(ERL_OBJECTS) $(addprefix $(EBIN_DIR)/, $(APP_FILES)) $(CTL_FILES)

$(EBIN_DIR)/%.app: %.app.src
	@echo "creating .app file... "
	@sed -e 's:@APP_VSN@:$(APP_VSN):g' \
		-e 's:@MODULES@:$(ERL_MODULES_IN_APP):g' -e "s:\`:\':g" -e 's/,]/]/g' $< > $@

$(BASE_DIR)/%ctl: %ctl.tpl
	@sed -e 's:@LOGDIR@:$(LOGDIR):g' -e 's:@CONFDIR@:$(CONFDIR):g'  $< > $@
	(chmod 775 $@)

$(EBIN_DIR)/%.$(EMULATOR): %.erl $(INCLUDE_FILES)
	$(ERLC) $(ERLC_FLAGS) -o $(dir $(@)) $<

all: $(ALL_FILES)

edoc:
	$(ERL) -noshell -eval "edoc:application($(APP_NAME), \".\", [${DOC_OPTS}]),init:stop()"

clean:
	(rm -rf ./*.beam; rm -rf $(EBIN_DIR)/*.beam; rm -f $(EBIN_DIR)/*.app; rm -f ../$(APP_NAME)ctl)

all: $(EBIN_FILES)
