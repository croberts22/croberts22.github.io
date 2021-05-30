---
layout: default
title:  "Moving Fast with Fastlane"
author: "Corey Roberts"
permalink: /blog/moving-fast-with-fastlane.html
published: true
summary: "fastlane is an incredible tool that helps automate several tasks in the world of mobile development. As someone who's used it for 5+ years, I wanted to highlight some tips and tricks that have helped me become even more agile with using fastlane."
---

[fastlane](https://fastlane.tools/) is an incredible tool that helps automate several tasks in the world of mobile development. First started off as a small Ruby project to automate the nuances in iOS development heralded by [Felix Krause](https://twitter.com/krausefx), it's since grown into a rich, robust toolset that can be used for the mobile development community. I discovered fastlane when I needed to find a solution for deploying 12 apps that had near-identical deployment setups for work. I had previously relied on a Perl script to deploy items iteratively, but you could imagine trying to deploy that many apps with a simple Perl script on a local machine only spells fragility with a side of disaster. After setting up a Fastfile, I was able to automate this on build machines in parallel in about a week with relatively low effort and knowledge on Ruby. 

Since then, I've created numerous custom lanes, actions, and plugins that have supported a variety of capabilities, including publishing version bump commits, writing a wrapper for Carthage to optimize building/caching frameworks, and optimizing ways to add devices and provisioning profiles to the developer portal. The versatility of fastlane really is deep, and I'd love to showcase how you can optimize your configuration and files for extensibility and reusability! This first entry in this entry will talk about how we can make elegant, composable lanes. Later entries will talk about leveraging environments and using some of the more lesser-known gems of the Fastlane universe to make robust, powerful command line tools.

For me, I enjoy learning by using an example. We'll be working with a fastlane setup that requires supporting publishing an app using two beta lanes: one for internal, another for external. We'll go from a simple, out-of-the-box solution and work our way down to creating reusable components with validation and useful output. Let's adventure, shall we? ⛵

{{TOC}}

# Initial Setup

Let's consider the example we just described with having two distribution lanes for betas. Both require actions that are identical, minus some build settings and metadata depending on the beta group we're deploying to. Some actions could include things like:

- Verifying the repo isn't dirty first
- Setting up provisioning profiles
- Checking for the latest build version on TestFlight and bumping it if needed
- Building the app 
- Uploading the app to TestFlight

The beauty of fastlane is that you can build things out-of-the-box, no extra fanfare required! 🎉 For instance, to build out our steps without any additional files or tools, we can write the following:

```ruby
platform :ios do

  before_all do
    ENV['XCODE_PATH'] = '/path/to/xcode/project'
  end
  
  desc 'Submits a new internal beta to TestFlight.'
  lane :internal_beta do
	
    if is_ci? == false
      ensure_git_status_clean
      reset_git_repo
    end
	    
    match(
      git_url: "git@github.com:myanilist.git",
      type: "appstore",
      app_identifier: "com.coreyroberts.myanilist",
      username: "my@email.com"
    )

    build_number = latest_testflight_build_number(
      version: get_version_number(xcodeproj: ENV['XCODE_PATH'], target: 'MyAniList').to_s,
      initial_build_number: get_build_number(xcodeproj: ENV['XCODE_PATH'])
    )
	
    increment_build_number(
      build_number: build_number.to_i + 1,
      xcodeproj: 'MyAniList.xcodeproj'
    )
	  
    gym(scheme: 'MyAniList-Beta')
    pilot(groups: 'Internal')
	  
  end
	
end
```

For setting up the repo, provisioning profiles, deployment schemes, and submission, this is quite succinct! However, this only supports one distribution group, our internal group. We can create another lane for our external group like such:

```ruby
platform :ios do

  before_all do
    ENV['XCODE_PATH'] = '/path/to/xcode/project'
  end
  
  desc 'Submits a new internal beta to TestFlight.'
  lane :internal_beta do
	
    if is_ci? == false
      ensure_git_status_clean
      reset_git_repo
    end
	    
    match(
      git_url: "git@github.com:myanilist.git",
      type: "appstore",
      app_identifier: "com.coreyroberts.myanilist",
      username: "my@email.com"
    )

    build_number = latest_testflight_build_number(
      version: get_version_number(xcodeproj: ENV['XCODE_PATH'], target: 'MyAniList').to_s,
      initial_build_number: get_build_number(xcodeproj: ENV['XCODE_PATH'])
    )
	
    increment_build_number(
      build_number: build_number.to_i + 1,
      xcodeproj: 'MyAniList.xcodeproj'
    )
	  
    gym(scheme: 'MyAniList-Beta')
    pilot(groups: 'Internal')
	  
  end
  
  desc 'Submits a new external beta to TestFlight.'
  lane :external_beta do
	
    if is_ci? == false
      ensure_git_status_clean
      reset_git_repo
    end
	    
    match(
      git_url: "git@github.com:myanilist.git",
      type: "appstore",
      app_identifier: "com.coreyroberts.myanilist",
      username: "my@email.com"
    )

    build_number = latest_testflight_build_number(
      version: get_version_number(xcodeproj: ENV['XCODE_PATH'], target: 'MyAniList').to_s,
      initial_build_number: get_build_number(xcodeproj: ENV['XCODE_PATH'])
    )
	
    increment_build_number(
      build_number: build_number.to_i + 1,
      xcodeproj: 'MyAniList.xcodeproj'
    )
	  
    gym(scheme: 'MyAniList-Beta')
    pilot(groups: 'External')
	  
  end
	
end
```

We could set up two lanes with nearly-identical behavior and call it a day. If you don't think this would ever change, then this solution is probably satisfactory. However, if you were to write something like this in code, you might think: we could do better! After all, if we changed one lane, we may forget to update the other. What if we change the scheme name? What if we wanted to modify what build version gets set prior to building the app? The more we duplicate, the greater the chances that we forget to keep the behavior consistent across lanes.

# Composing Lanes

## Creating a Lane

One of the great things about `fastlane` is that each lane acts similar to methods. Being able to compose lanes with smaller lanes makes it incredibly flexible and easy to reuse behavior if two lanes are similar-yet-different. 

A way to get behind this is to make a separate lane that other lanes can call. Let's create a new `beta` lane:

```ruby
desc 'Submits a new beta to TestFlight.'
lane :beta do 
  if is_ci? == false
    ensure_git_status_clean
    reset_git_repo
  end
    
  match 
  update_build_number
  gym(scheme: 'MyAniList-Beta')
  pilot(groups: ??????)
end
```

In our example, our current motivation is to create two paths for distributing betas. Invoking the preconditions, setup, build actions, and mechanism for distributing are in fact, identical. The only thing that's really changed is the group name (shown with `??????` above). What if instead, we passed the group name as a parameter? But how would you do that with a lane? 🤔 

## The Ruby Options Hash

Fortunately for us, `fastlane` allows for custom parameters that can be passed to a lane. This behavior is similar to native Ruby's `option` dictionary pattern, where one can pass in an `options` dictionary ("hash" in Ruby lexicon) with any extra parameters that need to be declared, and it is up to the callee's invocation to interpret or ignore those parameters. In `fastlane`, it is as simple as adding the following to a lane definition:

```ruby
desc 'Submits a new beta to TestFlight.'
lane :beta do |options|
# ...
end
```

The wonderful part about this is that because it's a dictionary, you can define whatever keys you want without needing to modify the method definition. Because it's a dictionary, you can ask for any key that's important to the callee. Before we start using it though, let's figure out what keys we'll define. For everyone's sanity, it would behoove us to write some documentation so people know what is considered valid output. Let's use `group` to denote the name of the beta group. We can add another description line above the lane:

```ruby
desc 'Submits a new beta to TestFlight.'
desc '- Parameter `group`: Defines the TestFlight group the beta should be distributed to.'
lane :beta do |options|
# ...
end
```

Now that we've done that, we can pull the value from the `options` dictionary. In Ruby syntax, we can use `options[:group]` to get the value stored in the dictionary defined by the key `group`:

```ruby
desc 'Submits a new beta to TestFlight.'
desc '- Parameter `group`: Defines the TestFlight group the beta should be distributed to.'
lane :beta do |options|
  if is_ci? == false
    ensure_git_status_clean
    reset_git_repo
  end
    
  match 
  update_build_number
  gym(scheme: 'MyAniList-Beta')
  pilot(groups: options[:group])
end
```

And there you have it! Two lanes have been turned into one, and we can now pass in a parameter for the group we want to publish our beta to! 🎉 

## Invoking Parameters 

### Command Line

If we wanted to, we could keep this lane as our canonical beta lane and require the developer to be responsible in passing in the argument. This could look something like this in the command line:

```bash
$> bundle exec fastlane beta group:"Internal"
$> bundle exec fastlane beta group:"External"
```

When using the command line, you pass in the name of the key you expect the lane to understand, followed by a colon and the argument. This is perfect if you have a parameter whose values may be arbitrary and you require the developer to specify it. If you have multiple parameters, you can attach them similar to how most command line tools operate. The following assumes that the `beta` lane can handle two parameters, `group` and `scheme`:

```bash
$> bundle exec fastlane beta group:"Internal" scheme:"MyAniList-Beta"
```

### Within the Fastfile

It may be useful to invoke a lane with arguments from within another lane. This looks and behaves similarly to a method invocation: The keys for the dictionary act like parameters. For our `beta` lane, we simply style it as if `group` was a parameter, as shown in our `internal_beta` lane:

 ```ruby
desc 'Submits a new beta to the "Internal" group to TestFlight.'
lane :internal_beta do
    beta(group: "Internal")
end

desc 'Submits a new beta to TestFlight.'
desc '- Parameter `group`: Defines the TestFlight group the beta should be distributed to.'
lane :beta do |options|
    # ...
end
```

# Guarding Parameters

Parameters are useful when the arguments are meaningful. Just like with any programming language, we often have to consider how we can defend a method so it can accept useful input and reject useless ones. The most common cases for this include empty, `nil`/`null`, or out-of-bounds values. `fastlane` provides some convenient APIs to throw errors and alert the user with an informative message in case an action or value passed in is unintentional. 

## The UI Helper Class

You can emit messages using methods from the `UI` helper class. There's extensive documentation on the different kinds of messages you can emit [here](https://docs.fastlane.tools/advanced/actions/#interacting-with-the-user). 

In our example, we could utilize `UI.user_error` as a way to notify if using our `beta` lane doesn't include the required parameter `group`, as such:

```ruby
desc 'Submits a new beta to TestFlight.'
desc '- Parameter `group`: Defines the TestFlight group the beta should be distributed to.'
lane :beta do |options|
    # Fail fast if `group` doesn't exist!
    UI.user_error!("`group` parameter is required.") unless options[:group]

    group = options[:group]

    # Let the dev know we're getting ready to build... 🚧
    UI.message "Configurating build for Crashlytics group \"#{group}\"..."
    
    ...
end
```

We include `UI.message` here to inform the runner that something is going to happen. This isn't required, but leaving useful messages is always a great indicator to show that *something* is happening. 🙂

Instead of guarding for invalid input through lanes, what if you wanted to try to resolve it by providing a prompt to the engineer? You can do this easily by assigning a value to the output of `UI.input`. It will wait for any input from stdin and then return that as the value that you can then use for your lanes, like such:

```ruby
group = UI.input("What group do you want to submit this beta to? (ex: \"Internal\", \"External\", etc.)")
UI.message "User wants to submit to \"#{group}\"!"
```

# Considering Your Fastlane API

In our beta example, it might actually make sense to make multiple lanes for our beta channels. The API invocation from both the developer and CI perspectives would make it easy to remember if we had two lanes called `internal_beta` and `external_beta`. 

Ultimately, this is your choice and depends on how you use your lanes. A good rule of thumb is to consider how you'd write it in code! More lanes might be useful, but consider your internal best practices: if having so many lanes public feels right, if it starts to feel complex or confusing, and so on. 

For the sake of this example, let's follow this pattern and see what this looks like:

 ```ruby

desc 'Submits a new beta to the "Internal" group to TestFlight.'
lane :internal_beta do
    beta(group: "Internal")
end

desc 'Submits a new beta to the "External" group to TestFlight.'
lane :external_beta do
    beta(group: "External")
end

desc 'Submits a new beta to TestFlight.'
desc '- Parameter `group`: Defines the TestFlight group the beta should be distributed to.'
lane :beta do |options|
    # ...
end
```

This is looking great, and it's pretty easy to remember. Given we only have two groups to consider, this feels like a good use case of having multiple lanes for a given action. We'll keep it for now! If we ever end up having three or more groups pop up ad hoc, we can always revert back to our `beta` lane.

## Private Lanes

Now that you have lanes that are composed with smaller, functional lanes, how do you obfuscate lanes that might not make sense to run on their own? In our example, what if we wanted to hide the fact that there's a generic `beta` lane? Maybe we want to prevent someone from running this command if the group "Wortwortwort" didn't exist (which it doesn't). How would we do that?

As engineers who care about creating well-written and thoughtful architecture, we care about the visibility of your classes, structs, and properties. We often use scope modifiers like `internal` and `private` to denote possible concerns on API usage, access safety, and unintended behavioral problems if the scope is too broad, to enumerate a few use cases. 

`fastlane` allows for two kinds of scope modifiers: public and private. By default, all lanes are defined implicitly as public. There may be times where you have a lane that only performs a certain task within another lane, and running it standalone either doesn't make sense or will throw an error. For example, perhaps you have a lane set up to create a commit message to bump the version number of the Xcode project. By itself, it requires context; but, its usage is perfectly warranted when publishing betas or release builds. In our beta example, there's some context needed, but having the ability to pass in any value feels a bit loose from an API standpoint. 

We can hide this lane for usage only within public lanes by using the `private_lane` attribute. Here, we illustrate both examples:

```ruby

desc 'Creates a new version and publishes the commit.'
lane :create_new_version do |options|

  # Grab values necessary to publish version information.
  branch = git_branch
  version_number = options[:version]
  codename = options[:codename]
  
  # Modifies the Xcode project so it can increment the version number.
  increment_version_number(version_number: version_number)

  # Creates a commit using this private lane.
  publish_version_bump(
	  version_number: version_number, 
	  codename: codename,
	  branch: branch)
    
end

desc 'Publishes a commit with the build version and the codename.'
private_lane :publish_version_bump |options| do

  version_number = options[:version_number]
  codename = options[:codename]
  branch = options[:branch].to_s

  commit_version_bump(
    message: "[version] Bumping version to #{version_number} with codename \"#{codename}\".",
    xcodeproj: 'MyAniList.xcodeproj'
  )
  
  # Push the commit.
  push_to_git_remote(remote_branch: branch)
end

```

We create a private lane for publishing a version bump since we want to prevent any "public" use of that API. Going back to our main example, all it takes it modifying our `beta` lane to use `private_lane`:

 ```ruby

desc 'Submits a new beta to the "Internal" group to TestFlight.'
lane :internal_beta do
    beta(group: "Internal")
end

desc 'Submits a new beta to the "External" group to TestFlight.'
lane :external_beta do
    beta(group: "External")
end

desc 'Submits a new beta to TestFlight.'
desc '- Parameter `group`: Defines the TestFlight group the beta should be distributed to.'
private_lane :beta do |options|
    # ...
end
```

And that's all there is to it!

# Recap

We took a beta workflow that could be built through fastlane and figured out how we could break it down meaningfully. We started with two lanes, then figured out how to solve it with one by using parameters. Then, we took it even further and created simple "subclassed"-esque lanes so our API could be easier to use from an engineering and CI standpoint. Finally, we took a brief look at how we can clean up our API so unintentional usage of lanes or variables could be mitigated.

The sample Fastfile we worked on is available in my Github repository [here](https://github.com/croberts22/website-examples/blob/main/fastlane/MovingFast_P1_Fastfile). I'd love to see what you come up with! 

The next entry in this series will dig deeper into environments and gems within the Fastlane ecosystem to build out robust and powerful tools that you and your teams can use.

Until next time. ✌️
