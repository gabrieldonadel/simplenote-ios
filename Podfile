project_root = File.join(__dir__, 'exp')
ENV['PROJECT_ROOT'] = project_root

require File.join(File.dirname(`node --print "require.resolve('expo/package.json')"`), "scripts/autolinking")
require File.join(File.dirname(`node --print "require.resolve('react-native/package.json')"`), "scripts/react_native_pods")

# frozen_string_literal: true

source 'https://cdn.cocoapods.org/'

raise 'Please run CocoaPods via `bundle exec`' unless %w[BUNDLE_BIN_PATH BUNDLE_GEMFILE].any? { |k| ENV.key?(k) }

inhibit_all_warnings!
use_frameworks!

platform :ios, '15.1'
workspace 'Simplenote.xcworkspace'

prepare_react_native_project!

# Main
#
abstract_target 'Automattic' do
  # Main Target
  #
  target 'Simplenote' do
    use_expo_modules!({projectRoot: project_root})

    config_command = [
      'npx',
      'expo-modules-autolinking',
      'react-native-config',
      '--json',
      '--platform',
      'ios',
      '--project-root',
      project_root,
      '--source-dir',
      "#{__dir__}"
    ]
    config = use_native_modules!(config_command)

    use_react_native!(
      :path => config[:reactNativePath],
      # An absolute path to your application root.
      :app_path => "#{project_root}",
    )

    # Automattic
    #
    pod 'Simperium', '1.9.0'

    # Testing Target
    #
    target 'SimplenoteTests' do
      inherit! :search_paths
    end

    # Post Install
    #
    post_install do |installer|
      react_native_post_install(
        installer,
        config[:reactNativePath],
        :mac_catalyst_enabled => false,
      )

      installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|
          # Remove min deploy target to clean up build warnings.
          # See: https://stackoverflow.com/a/64048124
          config.build_settings.delete 'IPHONEOS_DEPLOYMENT_TARGET'
          # Fix a code signing issue in Xcode 14 beta.
          # This solution is suggested here: https://github.com/CocoaPods/CocoaPods/issues/11402#issuecomment-1189861270
          config.build_settings['CODE_SIGN_IDENTITY'] = ''
        end
      end
    end
  end
end