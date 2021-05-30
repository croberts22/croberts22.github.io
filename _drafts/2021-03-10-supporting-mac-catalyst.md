2021-03-10-supporting-mac-catalyst

Problems encountered:
- Provisioning, had to add device; switched to automatic 
- Firebase Analytics does not work with Mac Catalyst
- Injecting git commit fails due to derived data location
	- https://stackoverflow.com/questions/27145958/abc-app-unsealed-contents-are-present-in-the-bundle-root-xcode-any-change-req
	- Adjust to point to /Contents/ for macOS PLATFORM_NAME
- Finally builds!
	- Error:
	- ```2021-03-10 23:44:11.562569-0600 MyAniList[95001:4383721] [General] An uncaught exception was raised
2021-03-10 23:44:11.562642-0600 MyAniList[95001:4383721] [General] UIRefreshControl is not supported when running Catalyst apps in the Mac idiom. Consider using a Refresh menu item bound to ⌘-R```
