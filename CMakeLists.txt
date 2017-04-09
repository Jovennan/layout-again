# nome do projeto
    project (layout-again)

    # a menor versão stável do cmake suportada
    cmake_minimum_required (VERSION 2.6)

    # informe ao cmake onde os módulos podem ser encontrados em nosso diretório de projeto
    list (APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)
    list (APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake/src)

    # onde nós instalamos o diretório data (se tivermos algum)
    set (DATADIR "${CMAKE_INSTALL_PREFIX}/share")

    # o que chamar esse diretório onde nós instalamos dados também
    set (PKGDATADIR "${DATADIR}/layout-again")

    set (EXEC_NAME "layout-again")
    set (RELEASE_NAME "Um hello GTK Layout.")
    set (VERSION "0.2")
    set (VERSION_INFO "Como usar Layout GTK com Vala")

    # we're about to use pkgconfig to make sure dependencies are installed so let's find pkgconfig first
    find_package(PkgConfig)

    # now let's actually check for the required dependencies
    pkg_check_modules(DEPS REQUIRED gtk+-3.0)

    add_definitions(${DEPS_CFLAGS})
    link_libraries(${DEPS_LIBRARIES})
    link_directories(${DEPS_LIBRARY_DIRS})

    # make sure we have vala
    find_package(Vala REQUIRED)
    # make sure we use vala
    include(ValaVersion)
    # make sure it's the desired version of vala
    ensure_vala_version("0.16" MINIMUM)

    # files we want to compile
    include(ValaPrecompile)
    vala_precompile(VALA_C ${EXEC_NAME}
        src/layout-again.vala

    # tell what libraries to use when compiling
    PACKAGES
        gtk+-3.0
    )

    # tell cmake what to call the executable we just made
    add_executable(${EXEC_NAME} ${VALA_C})

    # install the binaries we just made
    install (TARGETS ${EXEC_NAME} RUNTIME DESTINATION bin)

    # install our .desktop file so the Applications menu will see it
    install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.jovennan.layout-again.desktop DESTINATION ${DATADIR}/applications/)

    # install our .appdata.xml file so AppCenter will see it
    install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.jovennan.layout-again.appdata.xml DESTINATION ${DATADIR}/metainfo/)

    # Translation files
set (GETTEXT_PACKAGE "${CMAKE_PROJECT_NAME}")
add_definitions (-DGETTEXT_PACKAGE=\"${GETTEXT_PACKAGE}\")
add_subdirectory (po)
