# CJPAdController 1.0

CJPAdController is a singleton class allowing easy implementation of iAds and Google AdMob ads in your iOS app. It supports all devices and orientations, and works on iOS 4.2+

## Features
* Supports iPhone, iPod touch and iPad, in any orientation
* Choose whether to show both iAd and AdMob, or just one of them
* Choose whether iAd or AdMob is your default ads, falling back to the other if your default is unable to load an ad
* Specify a time delay for when to start showing ads in a view
* Automatically hides from view if there are no ads to display
* Support for hiding ads from users who have purchased a "Remove Ads" In-App Purchase (assumes you store a boolean value for this in `NSUserDefaults`)

## Usage

CJPAdController is intended to be used within a navigation controller, and will display adverts at the bottom of your view. It may work in other scenarios, such as within a TabBarController, however this has not been tested.

Add both `CJPAdController.h` and `CJPAdController.m` to your project. Before going any further, be sure to modify the constants at the top of `CJPAdController.h` - here you'll be able to set various options, including your AdMob Publisher ID. 

`#import "CJPAdController.h"` in your view controller, then add the following line of code to the `viewWillAppear` method in your `UIViewController`:

```objective-c
- (void)viewWillAppear:(BOOL)animated
{
    // Add ads to our view
    [[CJPAdController sharedManager] addBannerToViewController:self];
}
```

If your app supports both portrait and landscape orientations, you'll also need to add the following lines to the  willRotate and didRotate methods in your `UIViewController`:

```objective-c
- (void)willAnimateRotationToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
{
    [[CJPAdController sharedManager] rotateAdToInterfaceOrientation:toInterfaceOrientation];
    [super willAnimateRotationToInterfaceOrientation:toInterfaceOrientation duration:duration];
}

- (void)didRotateFromInterfaceOrientation:(UIInterfaceOrientation)fromInterfaceOrientation
{
    [[CJPAdController sharedManager] fixAdViewAfterRotation];
    [super didRotateFromInterfaceOrientation:fromInterfaceOrientation];
}
```

## Other Requirements
In order to compile, you'll need to have these frameworks included in your project:
For iAd:
  1. `iAd.framework`
  
For AdMob:
  1. `AudioToolbox.framework`
  2. `MessageUI.framework`
  3. `SystemConfiguration.framework`
  
NOTE: For AdMob to work ensure you also include the [AdMob SDK](https://developers.google.com/mobile-ads-sdk/download#downloadios) which is a set of header files and a library. CJPAdController should have no problems working with past, present or future versions of the SDK. Version 5.0.8 is included in the demo app in this project.


## Notes
  1. This class has been written for projects using ARC, and as such, no memory management is handled by the class.
  2. If you offer your users an In-App Purchase for removing ads, this class does offer some functionality for you to provide this. Assuming you have your IAP methods all set up, here is an example code snippet of a method you could call after a user successfully buys your IAP:
```objective-c
- (void)removeAdvertising
{
    // Remove all ad banners from the view
	if([CJPAdController sharedManager].iAdView!=nil) [[CJPAdController sharedManager] removeBanner:@"iAd" permanently:YES];
    if([CJPAdController sharedManager].adMobView!=nil) [[CJPAdController sharedManager] removeBanner:@"AdMob" permanently:YES];
    
    // Set adsRemoved to YES, and store in UserDefaults for future use
    [CJPAdController sharedManager].adsRemoved = YES;
    [[NSUserDefaults standardUserDefaults] setBool:YES forKey:kAdsPurchasedKey];
    [[NSUserDefaults standardUserDefaults] synchronize];
}
```

## To-Do
Planned features to be added include:
* Add option to specify whether to position the ad at the top or the bottom of the view
* Find way of handling rotation automatically
* Add support (modular?) for additional ad networks
* Add optional feature for animating transitions


## Changes
###1.0
* Initial release


## Licence and Attribution
If you're feeling kind you can provide attribution and a link to [this GitHub project](https://github.com/chrisjp/CJPAdController), but you don't have to.

### Licence
Copyright (c) 2011-2012 Chris Phillips

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.