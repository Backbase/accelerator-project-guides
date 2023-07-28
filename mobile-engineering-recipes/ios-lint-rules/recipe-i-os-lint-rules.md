# Recipe - iOS Lint Rules
In order to avoid code smells, and enforce a common code convention and syntax, it is strongly recommended to use [SwiftLint](https://github.com/realm/SwiftLint "https://github.com/realm/SwiftLint") in your project. You can install it in many different ways such as installing in your machine or add it to your [CocoaPods](https://cocoapods.org/ "https://cocoapods.org/") repo. Check the following link to see how do you want to install it: [![](./recipe-i-os-lint-rules-0.png)GitHub - realm/SwiftLint: A tool to enforce Swift style and conventions.](https://github.com/realm/SwiftLint#installation)

### Adding to your project

After installing the SwiftLint, select the project in the file navigator, then select the primary app target, and go to Build Phases. Click the + and select "New Run Script Phase". Insert the following as the script:

 ```
 export PATH="$PATH:/opt/homebrew/bin"
 if which swiftlint > /dev/null; then
   swiftlint
 else
   echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
 fi
```

For more info, check [![](./recipe-i-os-lint-rules-1.png)GitHub - realm/SwiftLint: A tool to enforce Swift style and conventions.](https://github.com/realm/SwiftLint#xcode)

### Configuring the rules

SwiftLint comes with [default rules, as well as opt in rules](https://realm.github.io/SwiftLint/rule-directory.html "https://realm.github.io/SwiftLint/rule-directory.html"). You should configure it using the `.swiftlint.yml` file. It is recommended to use all the default rules. We also recommend to opt in the rules on the following configuration file:

 ```
 included: # Includes the folder which the lint rules should be applied
   - Sources
   
 excluded: # Exclude the folders which the lint rules should not be applied.
   - Carthage
   - Pods
   
 opt_in_rules: # some rules are turned off by default, so you need to opt-in
   - empty_count # Find all the available rules by running: `swiftlint rules`
   - convenience_type
   - empty_string
   - fatal_error_message
   - file_name
   - first_where
   - modifier_order
   - weak_delegate
   - prohibited_interface_builder # In case you're in a team that decided not to use Interface Builder
```

### Finding rules that are not followed currently

After installing and configuring SwiftLint, on Xcode build your application `Product > Build` (**⌘B**) and you might see lots of warnings/errors in your project where there is a rule violation.

### Add SwiftLint to your CI/CD

SwiftLint can be added to your CI/CD using Fastlane - [![](./recipe-i-os-lint-rules-2.png)swiftlint - fastlane docs](https://docs.fastlane.tools/actions/swiftlint/) . In your Fastlfile you can add a lane to run SwiftLint, such as:

 ```
 swiftlint(
   mode: :lint,      # SwiftLint mode: :lint (default) or :autocorrect
   path: "/path/to/lint",       # Specify path to lint (optional)
   output_file: "swiftlint.result.json", # The path of the output file (optional)
   config_file: ".swiftlint-ci.yml",     # The path of the configuration file (optional)
   files: [# List of files to process (optional)
     "AppDelegate.swift",
     "path/to/project/Model.swift"
   ],
   raise_if_swiftlint_error: true,      # Allow fastlane to raise an error if swiftlint fails
   ignore_exit_status: true    # Allow fastlane to continue even if SwiftLint returns a non-zero exit status
 )
```

> Don’t forget to install SwiftLint on the machine which is running your CI/CD!

### References:

*   [![](./recipe-i-os-lint-rules-3.png)Swift.org](https://www.swift.org/documentation/api-design-guidelines/)