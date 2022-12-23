log_info("Configuring unit_tests")
file(GLOB_RECURSE unit_tests_SOURCE_FILES "*.cpp")
add_executable(unit_tests)
target_sources(unit_tests PRIVATE ${unit_tests_SOURCE_FILES})
target_link_libraries(unit_tests PRIVATE global_settings gtest gmock)
target_include_directories(unit_tests PRIVATE ${ROOT_DIR}/include)

gtest_discover_tests(
  unit_tests [WORKING_DIRECTORY $<TARGET_FILE_DIR:unit_tests>]
)
