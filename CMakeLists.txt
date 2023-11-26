cmake_minimum_required(VERSION 3.20 FATAL_ERROR)

project(breezesierra)
set(PROJECT_VERSION "0.1")
set(PROJECT_VERSION_MAJOR 0)

include(WriteBasicConfigVersionFile)
include(FeatureSummary)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(QT_MIN_VERSION "6.6.0")
find_package(Qt6 ${QT_MIN_VERSION} CONFIG REQUIRED COMPONENTS DBus)
find_package(ECM 0.0.9 REQUIRED NO_MODULE)

set(CMAKE_MODULE_PATH ${ECM_MODULE_PATH} ${ECM_KDE_MODULE_DIR} ${CMAKE_SOURCE_DIR}/cmake)
include(ECMInstallIcons)
include(KDEInstallDirs)
include(KDECMakeSettings)
include(KDECompilerSettings NO_POLICY_SCOPE)
include(GenerateExportHeader)

# include(GtkUpdateIconCache)

# old stuff
add_definitions(-DTRANSLATION_DOMAIN="breeze_kwin_deco")

set(KF6_MIN_VERSION "5.27.0")
set(KDECORATION2_MIN_VERSION "5.27.80")

find_package(KF6 ${KF6_MIN_VERSION} REQUIRED COMPONENTS CoreAddons GuiAddons ConfigWidgets WindowSystem I18n KCMUtils)
find_package(KDecoration2 ${KDECORATION2_MIN_VERSION} REQUIRED)

# ### XCB
find_package(XCB COMPONENTS XCB)
set_package_properties(XCB PROPERTIES
    DESCRIPTION "X protocol C-language Binding"
    URL "https://xcb.freedesktop.org"
    TYPE OPTIONAL
    PURPOSE "Required to pass style properties to native Windows on X11 Platform"
)

# ################ configuration #################
configure_file(config-breeze.h.in ${CMAKE_CURRENT_BINARY_DIR}/config-breeze.h)

# ################ newt target #################
# ## plugin classes
# set(breezedecoration_SRCS
set(sierrabreeze_SRCS
    breezebutton.cpp
    breezedecoration.cpp
    breezeexceptionlist.cpp
    breezesettingsprovider.cpp
    breezesizegrip.cpp)

# kconfig_add_kcfg_files(breezedecoration_SRCS breezesettings.kcfgc)
kconfig_add_kcfg_files(sierrabreeze_SRCS breezesettings.kcfgc)

# ## config classes
# ## they are kept separately because they might move in a separate library in the future
# set(breezedecoration_config_SRCS
set(sierrabreeze_config_SRCS
    config/breezeconfigwidget.cpp
    config/breezedetectwidget.cpp
    config/breezeexceptiondialog.cpp
    config/breezeexceptionlistwidget.cpp
    config/breezeexceptionmodel.cpp
    config/breezeitemmodel.cpp
)

# set(breezedecoration_config_PART_FORMS
set(sierrabreeze_config_PART_FORMS
    config/ui/breezeconfigurationui.ui
    config/ui/breezedetectwidget.ui
    config/ui/breezeexceptiondialog.ui
    config/ui/breezeexceptionlistwidget.ui
)

# ki18n_wrap_ui(breezedecoration_config_PART_FORMS_HEADERS ${breezedecoration_config_PART_FORMS})
ki18n_wrap_ui(sierrabreeze_config_PART_FORMS_HEADERS ${sierrabreeze_config_PART_FORMS})

# ## build library
# add_library(breezedecoration MODULE
# ${breezedecoration_SRCS}
# ${breezedecoration_config_SRCS}
# ${breezedecoration_config_PART_FORMS_HEADERS})
add_library(sierrabreeze MODULE
    ${sierrabreeze_SRCS}
    ${sierrabreeze_config_SRCS}
    ${sierrabreeze_config_PART_FORMS_HEADERS})

target_link_libraries(sierrabreeze
    PUBLIC
        Qt6::Core
        Qt6::Gui
        Qt6::DBus
    PRIVATE
        KDecoration2::KDecoration
        KF6::ConfigCore
        KF6::CoreAddons
        KF6::ConfigWidgets
        KF6::GuiAddons
        KF6::I18n
        KF6::KCMUtils
        KF6::WindowSystem)

install(TARGETS sierrabreeze DESTINATION ${KDE_INSTALL_PLUGINDIR}/${KDECORATION_PLUGIN_DIR})

# install(FILES config/sierrabreezeconfig.desktop DESTINATION ${SERVICES_INSTALL_DIR})
# install(TARGETS breezedecoration DESTINATION ${PLUGIN_INSTALL_DIR}/org.kde.kdecoration2)
# install(FILES config/breezedecorationconfig.desktop DESTINATION  ${SERVICES_INSTALL_DIR})
