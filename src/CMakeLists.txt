set(KDE_INSTALL_INCLUDEDIR_PIM ${KDE_INSTALL_INCLUDEDIR}/KPim)
set(pkpass_srcs
    barcode.cpp
    boardingpass.cpp
    field.cpp
    location.cpp
    pass.cpp
)
ecm_qt_declare_logging_category(pkpass_srcs
    HEADER logging.h
    IDENTIFIER KPkPass::Log
    CATEGORY_NAME org.kde.pkpass
)

add_library(KPimPkPass SHARED ${pkpass_srcs})
add_library(KPim::PkPass ALIAS KPimPkPass)
generate_export_header(KPimPkPass BASE_NAME KPkPass)
set_target_properties(KPimPkPass PROPERTIES
    VERSION ${KPKPASS_VERSION_STRING}
    SOVERSION ${KPKPASS_SOVERSION}
    EXPORT_NAME PkPass
)
target_include_directories(KPimPkPass INTERFACE "$<INSTALL_INTERFACE:${KDE_INSTALL_INCLUDEDIR_PIM}>")
target_link_libraries(KPimPkPass
    PUBLIC
        Qt5::Gui
    PRIVATE
        KF5::Archive
)

ecm_generate_headers(KPkPass_HEADERS
    HEADER_NAMES
        Barcode
        BoardingPass
        Field
        Location
        Pass
    REQUIRED_HEADERS KPkPass_HEADERS
)

install(TARGETS KPimPkPass EXPORT KPimPkPassTargets ${INSTALL_TARGETS_DEFAULT_ARGS})
install(FILES
    ${KPkPass_HEADERS}
    ${CMAKE_CURRENT_BINARY_DIR}/kpkpass_export.h
    DESTINATION ${KDE_INSTALL_INCLUDEDIR_PIM}/KPkPass
)

if (NOT ANDROID)
    install(FILES application-vnd-apple-pkpass.xml DESTINATION ${XDG_MIME_INSTALL_DIR})
    update_xdg_mimetypes(${XDG_MIME_INSTALL_DIR})
endif()
