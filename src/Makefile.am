include Makefile.include

AM_CPPFLAGS += -I$(builddir) -fpermissive -ffloat-store -std=c++11 -D__STDC_LIMIT_MACROS -D__USE_MINGW_ANSI_STDIO

noinst_LIBRARIES = \
  libocore_server.a \
  libocore_common.a \
  libocore_cli.a
if ENABLE_WALLET
noinst_LIBRARIES += libocore_wallet.a
endif

bin_PROGRAMS =

if BUILD_BITCOIND
  bin_PROGRAMS += ocored
endif

if BUILD_BITCOIN_CLI
  bin_PROGRAMS += ocore-cli
endif

SUBDIRS = . $(BUILD_QT) $(BUILD_TEST)
DIST_SUBDIRS = . qt test
.PHONY: FORCE
# bitcoin core #
BITCOIN_CORE_H = \
  addrman.h \
  alert.h \
  allocators.h \
  base58.h bignum.h \
  bloom.h \
  chainparams.h \
  checkpoints.h \
  checkqueue.h \
  clientversion.h \
  coincontrol.h \
  coins.h \
  compat.h \
  core.h \
  crypter.h \
  db.h \
  hash.h \
  init.h \
  key.h \
  keystore.h \
  leveldbwrapper.h \
  limitedmap.h \
  main.h \
  miner.h \
  mruset.h \
  netbase.h \
  net.h \
  noui.h \
  protocol.h \
  rpcclient.h \
  rpcprotocol.h \
  rpcserver.h \
  script.h \
  serialize.h \
  sync.h \
  threadsafety.h \
  tinyformat.h \
  trie.h \
  trieview.h \
  txdb.h \
  txmempool.h \
  ui_interface.h \
  uint256.h \
  util.h \
  version.h \
  walletdb.h \
  wallet.h

JSON_H = \
  json/json_spirit.h \
  json/json_spirit_error_position.h \
  json/json_spirit_reader.h \
  json/json_spirit_reader_template.h \
  json/json_spirit_stream_reader.h \
  json/json_spirit_utils.h \
  json/json_spirit_value.h \
  json/json_spirit_writer.h \
  json/json_spirit_writer_template.h

obj/build.h: FORCE
	@$(MKDIR_P) $(abs_top_builddir)/src/obj
	@$(top_srcdir)/share/genbuild.sh $(abs_top_builddir)/src/obj/build.h \
	  $(abs_top_srcdir)
version.o: obj/build.h

libocore_server_a_SOURCES = \
  addrman.cpp \
  alert.cpp \
  bloom.cpp \
  checkpoints.cpp \
  init.cpp \
  keystore.cpp \
  leveldbwrapper.cpp \
  main.cpp \
  purge.cpp \
  e_pow.cpp \
  miner.cpp \
  net.cpp \
  noui.cpp \
  rpcblockchain.cpp \
  rpcmining.cpp \
  rpcmisc.cpp \
  rpcnet.cpp \
  rpcrawtransaction.cpp \
  rpcserver.cpp \
  txdb.cpp \
  txmempool.cpp \
  trieview.cpp \
  blockcache.cpp \
  $(JSON_H) \
  $(BITCOIN_CORE_H)

libocore_wallet_a_SOURCES = \
  db.cpp \
  crypter.cpp \
  rpcdump.cpp \
  rpcwallet.cpp \
  wallet.cpp \
  walletdb.cpp \
  $(BITCOIN_CORE_H)

libocore_common_a_SOURCES = \
  allocators.cpp \
  chainparams.cpp \
  trie.cpp \
  trieengine.cpp \
  core.cpp \
  hash.cpp \
  key.cpp \
  netbase.cpp \
  protocol.cpp \
  rpcprotocol.cpp \
  script.cpp \
  sync.cpp \
  util.cpp \
  version.cpp \
  triesync.cpp \
  hash/tiger.cpp \
  hash/haval.cpp \
  hash/ripemd.cpp \
  hash/whirlpool.cpp \
  hash/sha2.cpp \
  hash/sha2big.cpp  \
  hash/keccak.cpp \
  $(BITCOIN_CORE_H)

libocore_cli_a_SOURCES = \
  rpcclient.cpp \
  $(BITCOIN_CORE_H)

nodist_libocore_common_a_SOURCES = $(top_srcdir)/src/obj/build.h
#

# ocored binary #
ocored_LDADD = \
  libocore_server.a \
  libocore_cli.a \
  libocore_common.a \
  $(LIBLEVELDB) \
  $(LIBMEMENV) \
  -lpthread

if ENABLE_WALLET
ocored_LDADD += libocore_wallet.a
endif
ocored_SOURCES = ocored.cpp
#

if TARGET_WINDOWS
ocored_SOURCES += ocored-res.rc
endif

AM_CPPFLAGS += $(BDB_CPPFLAGS)
ocored_LDADD += $(BOOST_LIBS) $(BDB_LIBS) -lgmp

ocored_LDFLAGS = -Wl,--allow-multiple-definition

# ocore-cli binary #
ocore_cli_LDADD = \
  libocore_cli.a \
  libocore_common.a \
  $(BOOST_LIBS) -lgmp
ocore_cli_SOURCES = ocore-cli.cpp
#

if TARGET_WINDOWS
ocore_cli_SOURCES += ocore-cli-res.rc
endif

# NOTE: This dependency is not strictly necessary, but without it make may try to build both in parallel, which breaks the LevelDB build system in a race
leveldb/libleveldb.a: leveldb/libmemenv.a

leveldb/%.a:
	@echo "Building LevelDB ..." && $(MAKE) -C $(@D) $(@F) CXX="$(CXX)" \
	  CC="$(CC)" PLATFORM=$(TARGET_OS) AR="$(AR)" $(LEVELDB_TARGET_FLAGS) \
	  OPT="$(CXXFLAGS) $(CPPFLAGS)"

qt/ocorestrings.cpp: $(libocore_server_a_SOURCES) $(libocore_common_a_SOURCES) $(libocore_cli_a_SOURCES)
	@test -n $(XGETTEXT) || echo "xgettext is required for updating translations"
	@cd $(top_srcdir); XGETTEXT=$(XGETTEXT) share/qt/extract_strings_qt.py

CLEANFILES = leveldb/libleveldb.a leveldb/libmemenv.a *.gcda *.gcno liblmdb/liblmdb.a liblmdb/libldb.so liblmdb/*.o

DISTCLEANFILES = obj/build.h

EXTRA_DIST = leveldb Makefile.include

clean-local:
	-$(MAKE) -C leveldb clean
	rm -f leveldb/*/*.gcno leveldb/helpers/memenv/*.gcno liblmdb/*/*.gcno
