include_directories(..)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../../test)

add_library(cachetester
  tester.cc
  testerstorage.cc
  testerlrustorage.cc
  testerrawstorage.cc
  teststorage.cc
  ../../../../../query_classifier/test/testreader.cc
  )

# Depends on C/C
add_dependencies(cachetester connector-c)

add_executable(testrules testrules.cc ../rules.cc)
target_link_libraries(testrules maxscale-common ${JANSSON_LIBRARIES})

add_executable(testkeygeneration
  testkeygeneration.cc
  ../../../../../query_classifier/test/testreader.cc
  )
target_link_libraries(testkeygeneration cachetester cache maxscale-common)

add_executable(testrawstorage testrawstorage.cc)
target_link_libraries(testrawstorage cachetester cache maxscale-common)

add_executable(testlrustorage testlrustorage.cc)
target_link_libraries(testlrustorage cachetester cache maxscale-common)

add_executable(test_cacheoptions
  test_cacheoptions.cc

  ../../test/filtermodule.cc
  ../../test/mock.cc
  ../../test/mock_backend.cc
  ../../test/mock_client.cc
  ../../test/mock_dcb.cc
  ../../test/mock_routersession.cc
  ../../test/mock_session.cc
  ../../test/module.cc
  ../../test/queryclassifiermodule.cc
  )
target_link_libraries(test_cacheoptions maxscale-common)

add_test(TestCache_rules testrules)

add_test(TestCache_inmemory_keygeneration testkeygeneration storage_inmemory ${CMAKE_CURRENT_SOURCE_DIR}/input.test)
#add_test(TestCache_rocksdb_keygeneration testkeygeneration storage_rocksdb ${CMAKE_CURRENT_SOURCE_DIR}/input.test)

#usage: testrawstorage storage-module [threads [time [items [min-size [max-size]]]]]\n"
add_test(TestCache_storage_inmemory testrawstorage storage_inmemory 0 10 1000 1024 1024000)
#add_test(TestCache_storage_rocksdb  testrawstorage storage_rocksdb  0 10 1000 1024 1024000)

#usage: testlrustorage storage-module [threads [time [items [min-size [max-size]]]]]\n"
add_test(TestCache_lru_inmemory testlrustorage storage_inmemory 0 10 1000 1024 1024000)
#add_test(TestCache_lru_rocksdb  testlrustorage storage_rocksdb  0 10 1000 1024 1024000)

add_test(TestCache_options test_cacheoptions)
