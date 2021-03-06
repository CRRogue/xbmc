include ../../Makefile.include
DEPS= ../../Makefile.include Makefile size-max.patch 02-darwin-getentropy.patch remove-weak_import-check-for-osx.patch add-dl-as-private-lib.patch

# lib name, version
LIBNAME=gnutls
VERSION=3.6.11.1
SOURCE=$(LIBNAME)-$(VERSION)
ARCHIVE=$(SOURCE).tar.xz

ifeq (darwin, $(findstring darwin, $(HOST)))
# darwins tar doesn't know about xz - so we need our native version of tar here
ARCHIVE_TOOL=$(ARCHIVE_TOOL_NATIVE)
CONFIGURE_HACKS = ac_cv_func_vfork_works=no
CONFIGURE_HACKS+= ac_cv_func_fork=no
endif

# configuration settings
CONFIGURE=./configure --prefix=$(PREFIX) --disable-shared --without-p11-kit --disable-nls --with-included-unistring \
                      --with-included-libtasn1 --enable-local-libopts --disable-doc --disable-tests --disable-guile \
                      --disable-tools $(CONFIGURE_HACKS)

LIBDYLIB=$(PLATFORM)/lib/.libs/lib$(LIBNAME).a

all: .installed-$(PLATFORM)

$(TARBALLS_LOCATION)/$(ARCHIVE):
	cd $(TARBALLS_LOCATION); $(RETRIEVE_TOOL) $(RETRIEVE_TOOL_FLAGS) $(BASE_URL)/$(ARCHIVE)

$(PLATFORM): $(TARBALLS_LOCATION)/$(ARCHIVE) $(DEPS)
	rm -rf $(PLATFORM)/*; mkdir -p $(PLATFORM)
	cd $(PLATFORM); $(ARCHIVE_TOOL) $(ARCHIVE_TOOL_FLAGS) $(TARBALLS_LOCATION)/$(ARCHIVE)
	cd $(PLATFORM); patch -p1 -i ../size-max.patch
	cd $(PLATFORM); patch -p1 -i ../02-darwin-getentropy.patch
	cd $(PLATFORM); patch -p1 -i ../remove-weak_import-check-for-osx.patch
	cd $(PLATFORM); patch -p1 -i ../add-dl-as-private-lib.patch
	cd $(PLATFORM); $(AUTORECONF) -vif
	cd $(PLATFORM); $(CONFIGURE)

$(LIBDYLIB): $(PLATFORM)
	$(MAKE) -C $(PLATFORM)

.installed-$(PLATFORM): $(LIBDYLIB)
	$(MAKE) -C $(PLATFORM) install
	touch $@

clean:
	$(MAKE) -C $(PLATFORM) clean
	rm -f .installed-$(PLATFORM)

distclean::
	rm -rf $(PLATFORM) .installed-$(PLATFORM)
