# alm-rest-client
   ALM REST API Integration in ruby. With the help of ALM REST CLIENT Ruby gem we can easily update our Automated Test results to QC/ALM TestLab. Follow the below simple Steps to Integrate the ALM REST CLIENT gem into any RUBY CUCUMBER Framework.

### Open a console and type :
==> In terminal or cmd
```sh
$ gem install ‘alm-rest-client’
$ gem install nokogiri-happymapper
$ gem install colorize
```
###### Close and reopen your console
Type irb  → Enter →   a irb command line will start
==> In irb
```sh
> require 'alm-rest-client' # you should get below response :
=> true

> ALM.encodeuserpass('ALM-USER-ID','ALM-PASSWORD')
Successfully created your encodeuserpass
=> "RzM0MzXXXXXXXXXXXXyMDE5"
```

### To test if your encodepass is successfully generated type (in irb cmd line)
==> In irb
```sh
> ALM::RestConnector.instance.init(Hash.new, 'qualitycenter.url', '80', 'Domain_name', 'project_name',"put here your encodepass") # you should get
Init Successful
=> nil

> ALM.isLoggedIn() # you should get
Logged in successfully !
=> nil
 
> ALM.manageSession() # you should get
=> true
```
### Open your web ui automation project / or any other cucumber project :
##### Open the env.rb file
==> In `env.rb` file
```sh 
require 'alm-rest-client'
encodepass= 'insert code from above.'
ALM::RestConnector.instance.init(Hash.new, 'qualitycenter.url', '80', 'Domain_name', 'project_name', encodepass)
[OR]
ALM::RestConnector.instance.init(Hash.new, 'qualitycenter.url', '80', 'Domain_name', 'project_name', nil, 'ALM-UserName', 'ALM-Password')
```
##### Open the hooks.rb file
==> In `hooks.rb` file
```sh
Before do |scenario|
   $scenario = scenario.name
end

After do |scenario|
 begin
    if scenario.respond_to?('scenario_outline')
        $scenario = scenario.scenario_outline
        puts "=====>>> #{scenario}"
    end
    status = ""
    screenshot_fullname = nil     
    image_desc = nil
    if scenario.failed?
        time = Time.now
        folder_name = File.join(Dir.pwd, 'screenshots', 'errors', $scenario)
        FileUtils.mkdir_p(folder_name) unless Dir.exist? folder_name
        filename = 'Error_' + time.day.to_s + '-' + time.month.to_s + '-' + time.year.to_s + '_' + time.hour.to_s + 'h' + time.min.to_s + 'm' + time.sec.to_s + 's.png'
        screenshot_fullname = folder_name + '/' + filename
         
        puts "\n#{$scenario} - FAILED\n".white.on_red
        status = "Failed"
        puts '# -----------------------------------------------------------------------'
        puts '                      * * * ERROR DETAILS * * *'
        puts '# -----------------------------------------------------------------------'
        puts "Current URL : #{URI.parse(page.driver.current_url).to_s}"
        puts "Folder name : #{folder_name}"
        puts "Filename    : #{screenshot_fullname}"
         
        begin
            page.save_screenshot(screenshot_fullname)
            embed(screenshot_fullname, 'image/png', 'Screenshot')
        rescue
            puts "Unable to take screenshot - 1"
        end
        page.driver.quit
        Capybara.reset_sessions!
    else
        puts "\n#{$scenario} - PASSED\n".white.on_green
        status = "Passed"
    end
    if $scenario.include?(", Examples")
      str = $scenario.split(", Examples ")
      $scenario = str[0]
      image_desc = "Scenario Outline - Examples "+str[1]
    end
    # ALM.updateQC(TESTSET_FOLDER,TESTSET_NAME, $scenario, status, screenshot_fullname, image_desc)
    # ALM.updateQC(TESTSET_FOLDER,TESTSET_NAME, $scenario, status, screenshot_fullname, image_desc) --> this is location in testlab.     
    ALM.updateQC("Root\\ParentFolder\\TestSetFolder","TestSetName",$scenario,status,screenshot_fullname,image_desc)
    rescue
        puts "Unable to take screenshot for failed scenario - 2"
    end
end
```
For any queries about the products,
----
Feel Free to contact **[Yusuf Muhammed](https://github.com/myusufcse)**
