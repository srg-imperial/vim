SH     = $(shell which sh)
PWD    = $(shell pwd)

# Varan
VX     = $(shell which vx)

# Compilers
GCC    = $(shell which gcc)
CLANG  = $(shell which clang)

# For debugging
STRIP  = $(shell which true)
CFLAGS = -O0 -g

# VIM config flags
VIM_CFG_FLAGS=--disable-gui --without-x --with-features=huge

# Where each version expects to be installed
INSTALL_DIR       = $(PWD)/install
INSTALL_BUILD_DIR = $(INSTALL_DIR)/vim

# Where different versions are installed
INSTALL_GCC        = $(PWD)/install/gcc-system
INSTALL_GCC_ASAN   = $(PWD)/install/gcc-asan-system
INSTALL_CLANG      = $(PWD)/install/clang-system
INSTALL_CLANG_ASAN = $(PWD)/install/clang-asan-system

# Binaries inside each build directory
VIM_GCC        = $(INSTALL_GCC)/bin/vim
VIM_GCC_ASAN   = $(INSTALL_GCC_ASAN)/bin/vim
VIM_CLANG      = $(INSTALL_CLANG)/bin/vim
VIM_CLANG_ASAN = $(INSTALL_CLANG_ASAN)/bin/vim

# Script to run VIM with Varan
VIM_DIR = $(PWD)/bin
VIM = $(VIM_DIR)/vim

# Command lines to run VIM with Varan inside script
RUN_GCC_GCC          =                              $(VX) $(VIM_GCC) $(VIM_GCC)          -- \$$@
RUN_GCC_GCC_ASAN     = ASAN_OPTIONS="handle_segv=0" $(VX) $(VIM_GCC) $(VIM_GCC_ASAN)     -- \$$@
RUN_CLANG_CLANG      =                              $(VX) $(VIM_CLANG) $(VIM_CLANG)      -- \$$@
RUN_CLANG_CLANG_ASAN = ASAN_OPTIONS="handle_segv=0" $(VX) $(VIM_CLANG) $(VIM_CLANG_ASAN) -- \$$@

# Main target
all: vx-gcc-gcc_asan

# ==============================================================================
# GCC targets
# ==============================================================================

# Set up script for native leader and native follower built with gcc
vx-gcc-gcc: $(VIM_GCC)
	mkdir -p $(VIM_DIR) || true
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_GCC) $(INSTALL_BUILD_DIR)
	rm $(VIM) || true
	echo "#!$(SH)"        >  $(VIM)
	echo "$(RUN_GCC_GCC)" >> $(VIM)
	chmod a+x $(VIM)

# Set up script for native leader and sanitized follower built with gcc
vx-gcc-gcc_asan: $(VIM_GCC) $(VIM_GCC_ASAN)
	mkdir -p $(VIM_DIR) || true
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_GCC) $(INSTALL_BUILD_DIR)
	rm $(VIM) || true
	echo "#!$(SH)"             >  $(VIM)
	echo "$(RUN_GCC_GCC_ASAN)" >> $(VIM)
	chmod a+x $(VIM)

# Build using gcc and no sanitization
$(VIM_GCC):
	rm -rf $(INSTALL_BUILD_DIR) || true
	rm ../src/auto/config.cache || true
	make -C .. clean
	cd .. ; CC=$(GCC)             \
  	CFLAGS="$(CFLAGS)"            \
  	STRIP="$(STRIP)"              \
  	./configure                   \
  	--prefix=$(INSTALL_BUILD_DIR) \
	$(VIM_CFG_FLAGS)
	make -C ..
	make -C ../src install
	rm -rf $(INSTALL_GCC) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_GCC)

# Build using gcc and sanitization
$(VIM_GCC_ASAN):
	rm -rf $(INSTALL_BUILD_DIR) || true
	rm ../src/auto/config.cache || true
	make -C .. clean
	cd .. ; CC=$(GCC)                     \
  	CFLAGS="$(CFLAGS) -fsanitize=address" \
  	LDFLAGS="-fsanitize=address"          \
  	STRIP="$(STRIP)"                      \
  	./configure                           \
  	--prefix=$(INSTALL_BUILD_DIR)         \
	$(VIM_CFG_FLAGS)
	make -C ..
	make -C ../src install
	rm -rf $(INSTALL_GCC_ASAN) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_GCC_ASAN)

# ==============================================================================
# CLANG targets
# ==============================================================================

# Set up script for native leader and native follower built with clang
vx-clang-clang: $(VIM_CLANG)
	mkdir -p $(VIM_DIR) || true
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_CLANG) $(INSTALL_BUILD_DIR)
	rm $(VIM) || true
	echo "#!$(SH)"            >  $(VIM)
	echo "$(RUN_CLANG_CLANG)" >> $(VIM)
	chmod a+x $(VIM)

# Set up script for native leader and sanitized follower built with clang
vx-clang-clang_asan: $(VIM_CLANG) $(VIM_CLANG_ASAN)
	mkdir -p $(VIM_DIR) || true
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_CLANG) $(INSTALL_BUILD_DIR)
	rm $(VIM) || true
	echo "#!$(SH)"                 >  $(VIM)
	echo "$(RUN_CLANG_CLANG_ASAN)" >> $(VIM)
	chmod a+x $(VIM)

# Build using clang and no sanitization
$(VIM_CLANG):
	rm -rf $(INSTALL_BUILD_DIR) || true
	rm ../src/auto/config.cache || true
	make -C .. clean
	cd .. ; CC=$(CLANG)           \
  	CFLAGS="$(CFLAGS)"            \
  	STRIP="$(STRIP)"              \
  	./configure                   \
  	--prefix=$(INSTALL_BUILD_DIR) \
	$(VIM_CFG_FLAGS)
	make -C ..
	make -C ../src install
	rm -rf $(INSTALL_CLANG) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_CLANG)

# Build using clang and sanitization
$(VIM_CLANG_ASAN):
	rm -rf $(INSTALL_BUILD_DIR) || true
	rm ../src/auto/config.cache || true
	make -C .. clean
	cd .. ; CC=$(CLANG)                   \
  	CFLAGS="$(CFLAGS) -fsanitize=address" \
  	LDFLAGS="-fsanitize=address"          \
  	STRIP="$(STRIP)"                      \
  	./configure                           \
  	--prefix=$(INSTALL_BUILD_DIR)         \
	$(VIM_CFG_FLAGS)
	make -C ..
	make -C ../src install
	rm -rf $(INSTALL_CLANG_ASAN) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_CLANG_ASAN)

# ==============================================================================
# misc targets
# ==============================================================================

clean:
	make -C .. clean || true
	rm -rf $(INSTALL_DIR) || true
	rm -rf $(VIM_DIR) || true
