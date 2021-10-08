cmake_minimum_required(VERSION 3.0.0)

set(PROJECT_NAME "pg_store_plans") # 例: "PGroonga"
set(PROJECT_ID "pg_store_plans") # 例: "pgroonga"

set(VENDOR "horiguti") # 例: "The PGroonga Project"

set(VERSION_MAJOR "1") # 例: "0"
set(VERSION_MINOR "3") # 例: "5"
set(VERSION_PATCH "1")   # 例: "0"
# ↑は.controlから抽出することもできる
# 方法は後述するPGroongaの例を参照すること
set(VERSION_FULL "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")

project("${PROJECT_ID}")

# ↓はユーザーが指定した「%PostgreSQLをインストールしたフォルダー%」になる
set(POSTGRESQL_DIR "${CMAKE_INSTALL_PREFIX}"
  CACHE PATH "PostgreSQL binary directory")
# ↓はパッケージを作成するときだけ必要
set(POSTGRESQL_VERSION "9.6"
  CACHE STRING "PostgreSQL version")

set(LIBRARY_NAME "lib${PROJECT_ID}")

set(EXTENSION_DIR "lib")
set(EXTENSION_DATA_DIR "share/extension")
set(DOCUMENT_DIR "share/${PROJECT_ID}")

set(SOURCES
  "pg_store_plans.c"
  "pgsp_explain.c"
  "pgsp_json.c"
  "pgsp_json_text.c"
)

add_library("${LIBRARY_NAME}" SHARED ${SOURCES})
set_target_properties("${LIBRARY_NAME}"
   PROPERTIES
   OUTPUT_NAME "${PROJECT_ID}")

set_source_files_properties(${SOURCES}
  PROPERTIES
  COMPILE_FLAGS "/EHsc")

include_directories(
  "${POSTGRESQL_DIR}/include/server/port/win32_msvc"
  "${POSTGRESQL_DIR}/include/server/port/win32"
  "${POSTGRESQL_DIR}/include/server"
  "${POSTGRESQL_DIR}/include")

link_directories(
  "${POSTGRESQL_DIR}/lib")

target_link_libraries("${LIBRARY_NAME}"
  "postgres.lib")

install(TARGETS "${LIBRARY_NAME}"
  DESTINATION "${EXTENSION_DIR}")

install(FILES
  "${PROJECT_SOURCE_DIR}/${PROJECT_ID}.control"
  DESTINATION "${EXTENSION_DATA_DIR}")

install(FILES
  "${PROJECT_SOURCE_DIR}/${PROJECT_ID}--${VERSION_FULL}.sql"
  DESTINATION "${EXTENSION_DATA_DIR}")

set(CPACK_GENERATOR "ZIP")
set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY OFF)
set(CPACK_PACKAGE_VERSION_MAJOR "${VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${VERSION_PATCH}")
set(CPACK_PACKAGE_VENDOR "${VENDOR}")
if(CMAKE_CL_64)
  set(PACKAGE_SYSTEM_NAME "x64")
else()
  set(PACKAGE_SYSTEM_NAME "x86")
endif()
set(CPACK_PACKAGE_FILE_NAME
  "${PROJECT_ID}-${VERSION_FULL}-postgresql-${POSTGRESQL_VERSION}-${PACKAGE_SYSTEM_NAME}")

include(CPack)
