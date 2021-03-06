###############################################################################
# NAME:		    Makefile
#
# AUTHOR:	    Ethan D. Twardy <ethan.twardy@gmail.com>
#
# DESCRIPTION:	    Makefile for building apps container infrastructure
#
# CREATED:	    01/22/2022
#
# LAST EDITED:	    01/30/2022
###

ssoWheel = dist/djangobasicsso-0.1.0-py3-none-any.whl
BUILD_TARGETS += $(B)/$(ssoWheel)

appsWheel = dist/edtwardy_apps-0.1.0-py3-none-any.whl
BUILD_TARGETS += $(B)/$(appsWheel)

BUILD_TARGETS += $(B)/entrypoint.sh
BUILD_TARGETS += $(B)/uwsgi.ini
BUILD_TARGETS += $(B)/requirements.apps.txt

MAKEFILE_PATH := $(abspath $(lastword $(MAKEFILE_LIST)))
S := $(PWD)/$(notdir $(patsubst %/,%,$(dir $(MAKEFILE_PATH))))

# Everything before this line must be done before including this file
include ../declarations.mk
# And everyting after including this file must be after it...

django-sandbox/$(ssoWheel): $(shell find django-sandbox/basicsso) \
		django-sandbox/setup.py
	cd django-sandbox && python3 setup.py bdist_wheel

apps/$(appsWheel): $(shell find apps/apps) apps/setup.py apps/MANIFEST.in
	cd apps && python3 setup.py bdist_wheel

$(B)/$(ssoWheel): django-sandbox/$(ssoWheel); mkdir -p $(@D) && cp $< $@
$(B)/$(appsWheel): apps/$(appsWheel); mkdir -p $(@D) && cp $< $@
$(B)/Containerfile.apps: Containerfile.apps; cp $< $@
$(B)/entrypoint.sh: entrypoint.sh; cp $< $@
$(B)/uwsgi.ini: uwsgi.ini; cp $< $@
$(B)/requirements.apps.txt: requirements.apps.txt; cp $< $@

$(B)/apps-build.lock: $(B)/Containerfile.apps $(B)/build.lock
	$(call buildahBud,$<,apps)
	touch $@

containers: $(B)/apps-build.lock

install:
	install -d $(DESTDIR)/etc/volumetric/volumes.d
	install -m444 edtwardy-apps.yaml $(DESTDIR)/etc/volumetric/volumes.d

clean:
	rm -rf apps/edtwardy_apps.egg-info
	rm -rf apps/dist/
	rm -rf django-sandbox/djangoauthtest.egg-info
	rm -rf django-sandbox/djangobasicsso.egg-info

###############################################################################
