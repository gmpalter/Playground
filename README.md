#  About FirstWords

FirstWords is a suite of apps which teach toddlers and preschoolers about words and letters using a stimulating game.  These apps teach children about letters, how letters
relate to sounds, and even how to spell words.  The game includes beautiful illustrations, each matched with entertaining sounds and high-quality recordings of letters and words.
The UX is specifically designed for the smallest fingers.

The current roster of apps in chronological order of first release:

+ First Words Animals
+ First Words Deluxe
+ First Words Sampler
+ First Words Christmas
+ First Words Spanish
+ First Words Japanese
+ First Words Halloween
+ First Words Feelings
+ First Words Professional
+ First Sight Words Professional
+ First Words Homeschool
  
---


## Implementation Notes

### Hidden Configuration Parameters

The majority of the parameters which control the behavior and look of a FirstWords app are defined by the `Configuration` class.
Each of these parameters has documentation which can be accessed via Xcode's Quick Help facility.
However, there are some parameters which are defined elsewhere.

#### UX Colors

FirstWords apps use several non-standard colors for various UX elements (e.g., letter tile colors, purchase dialog button colors).
To make these colors easier to manipulate, they are maintained as named colors in `Common.xcassets`.

The UX colors are

+ Letter and Target tile colors -- Letter tiles have a single set of colors in `Letter Tiles/`. For target tiles, there are two sets of colors, `Target Tiles/Normal/` and `Target Tiles/Highlighted/`. The former are used for inactive targets and the latter for targets that are active (i.e., will accept the letter being dragged). Each set of colors has four entries.
    + `Background`
    + `Text`
    + `Highlight` -- Top and left  border colors
    + `Shade` -- Bottom and right border colors

- Purchase Dialog colors -- These colors are defined with both Light and Dark appearance variants. iOS automatically switches some of these colors as the system appearance changes. Others are switched in code by simply reassigning the named color to the view's `layer` in a `traitCollectionDidChange:` method. The named colors are
    - `Purchase Dialog/Header/Background`
    - `Purchase Dialog/Buttons/Background`
    - `Purchase Dialog/Buttons/Border` -- Assigned to the button's `layer.borderColor` property
    - `Purchase Dialog/Gradient/Start` -- Assigned to the gradient's `layer.colors[0]` property
    - `Purchase Dialog/Gradient/End` -- Assigned to the gradient's `layer.colors[1]` property

#### Home Screen UX

Several constraints that control the appearance of the home screen are created at runtime in the function `createConstraints` in `HomeScreenViewController.swift`. The appropriate constraints are then activated based on whether the app orientation is portrait or landscape in the `enableConstraints` function invoked by the screen's `viewDidLayoutSubviews` method.

The constraint parameters are

+ `HomeScreenViewController.titleImageWidthPortrait`
    + The width of the title as a fraction of the screen width when in portrait orientation
+ `HomeScreenViewController.titleImageWidthLandscape`
    + The width of the title as a fraction of the screen width when in landscape orientation

- `HomeScreenViewController.starburstCenterYPortrait`
    - The Y position of the starburst image as a fraction of the screen's center Y when  in portrait orientation
    - Smaller values place the image higher on the screen
- `HomeScreenViewController.starnurstCenterYLandscape`
    - The Y position of the starburst image as a fraction of the screen's center Y when  in landscape orientation
    - Smaller values place the image higher on the screen

+ `HomeScreenViewController.playImageWidthPortrait`
    + The width of the Play button image as a fraction of the screen width when in portrait orientation
+ `HomeScreenViewController.playImageHeightLandscape`
    + The height of the Play button image as a fraction of the screen height when in landscape orientation
+ `HomeScreenViewController.playImageCenterYPortrait`
    + The Y position of the Play button  image as a fraction of the screen's center Y when  in portrait orientation
    + Smaller values place the image higher on the screen
    + When in landscape orientation, the Play button is centered on the screen

- `HomeScreenViewController.buttonsBottomMargin`
    - The spacing between the More Games, Share, and Options buttons and the bottom of the screen when the Learning Touch logo is not visible
- `HomeScreenViewController.buttonsLogoSpacing`
    - The spacing between the More Games, Share, and Options buttons and the Learning Touch logo

Additionally, there are two UX parameters in `WordListTile.swift` which affect the appearance of locked word list tiles on the home screen.  They are

+ `WordListTile.lockedOverlayLocked`
    + The locked overlay opacity of word list tiles in the first expansion pack.
    + The current value, `0.2`, gives these locked tiles a slightly darker appearance than unlocked tiles

- `WordListTile.lockedOverlayLockedIncrement`
    - An increment added to the locked overlay opacity based on the expansion pack's position in the `Configuration.shared.expansionPacks` array.
    - A positive value causes locked word list tiles to appear darker as the expansion pack number increases

---


## TODO

+ Switch permanent expansion pack purchased status from local storage (`UserDefaults`) to the iCloud key-value store (`NSUbiquitousKeyValueStore`)?
    + Now that FW Deluxe  with IAP is live in the store, this change will require a migration strategy

- Add Chinese to all multi-language apps?

---


## Assets

FirstWords apps group words into categories of related words called word lists.  Some apps only have a single word list (e.g., FW Animals, FW Christmas)
while others offer multiple word lists (e.g., FW Deluxe, FW Japanese) and the home screen presents a UI to select the word list to play.  Once the child finishes a word list,
the app either returns to the home screen or displays a roadblock screen.

FirstWords apps may also offer word lists in multiple languages (e.g., FW Animals, FW Deluxe) or only offer a single language (FW Japanese).

All the data to render word lists in the game are contained in a set of related folders in the **Assets** group of the FirstWords project.  There are three subgroups

  + **Alphabets**: Assets for the app's supported languages
  + **Words**: Language-invariant assets (e.g., pictures)  for the app's word lists
  + **Localizations**: Language-dependent assets (e.g., spoken words) for the app's word lists

Each subgroup above contains multiple folders which hold the assets.  When added to the project, these folders must be added with the "Create folder references" option selected.


### `Alphabets` Group

The `Alphabets` group contains one folder for each language available in a FirstWords app.

The name of a language folder is the ISO 2-letter language code with optional variants added  separated by hyphens (-).  Examples of language names are

+ `en`: U.S. English
+ `en-phonics`: U.S. English using phonic sounds
+ `en-GB`: U.K. English
+ `en-GB-phonics`: U.K. English using phonics sounds
+ `ja`: Japanese

Each language folder contains

+ `all.txt`: The localized text for "All Words"
+ `other.txt`: The localized text for "Other…" -- this file is optional
+ `flag.png`: The image (115x79) of this language's main country's flag
+ `Letters`: A folder containing one or two sound files for each letter in the language.
    + If there is a different pronunciation or inflection based on if the letter is at the end of a word, the sound files are named `X-other.caf` and `X-final.caf`.
    + Otherwise, the sound file is `X.caf`.


### `Words` Group

The `Words` group contains the language invariant assets for each word list in a FirstWords app.  There is a single folder for each word list.  The name of the folder is the name of the word list.

Each word list folder contains

+ `icon.png`: The image (approximately 100x100) displayed on the home screen for this word list if the app supports multiple word lists (e.g., FirstWords Deluxe).
Device-specific variants of this image may also be included (e.g., `icon@3x~iphone.png`).
+ `picture`: A folder containing one image (680x680) for each word in the word list which depicts the word.
+ `background`: A folder containing one or more background images (1024x1024) used when playing this word list.  Each word in the word list defines its desired background in the localization folders.
+ `sound`: A  folder containing one or more sound files in CAF format which are the sounds played as part of the reward for properly spelling a word.
Again, each word in the word list defines its desired sound in the localization folders.

The images in the `picture` and `background` folders may be either PNG or PDF files.  PDF files are preferred for images which can be represented as scalable vector graphics.


### `Localization` Group

The `Localization` group contains the per-language assets for each word list in a FirstWords app.  There may be multiple folders for each word list if the app supports multiple languages.
The name of a folder is the word list name and the language name separated by an underscore (_).  Examples of  folder names are

+ `Animals_en`: U.S. English animal words
+ `Animals_en-phonics`: U.S. English animal words using phonics sounds
+ `Animals_en-GB`: U.K. English animal words
+ `Animals_en-GB-phonics`: U.K. English animal words using phonics sounds
+ `Animals_ja`: Japanese animal words

Each localization folder contains

+ `words.plist`: A standard Apple property list file containing an array of the words in this word list. (See below for details.)
+ `name.txt`: A file containing the localized name of this word list.
+ `Speech`: A folder containing one sound file, the spoken word, for each word in the word list in CAF format.  It is played if the child taps on the word's picture and also as part of the reward sequence.

If the localization folder is for a word list in an app with only one word list, the `name.txt` file may be omitted as it is not used.
If the localization folder is for a phonics language variant (e.g., "`Animals_en-phonics`"), the `name.txt` file may be omitted.  In that case, the `name.txt` file of the base language (e.g., "`Animals_en`") will be used.

If the localization folder is for a phonics variant of a language (e.g., "`Animals_en-phonics`") or a language with multiple written forms (e.g., "`Animals_zh-traditional`"), it may omit the `Speech` folder.
In that case, the `Speech` folder of the base language (e.g., "`Animals_en`" and "`Animals_zh`") will be used.

#### Free Word Lists

FirstWords apps which offer in-app purchases may also make subsets of some of the word lists in the in-app purchases available with the app to provide a taste of what's available to purchase.
These word list subsets are called free word lists.  They are represented in the `Localization` group as folders with a "`.free`" suffix after the word list name.  For example,

+ `Animals.free_en`: Subset of U.S. English animal words
+ `Animals.free_en-GB-phonics`: Subset of U.K. English animal words using phonics sounds

The localization folder for a free word list should not include the `name.txt` file or the `Speech` folder as the app will automatically find those assets in the full word list's localization folder.
Further, free word lists should not have a corresponding folder in the `Words` group as, again, the app will automatically use the assets in the full word list's `Words` group folder.

#### Contents of `words.plist`

The `words.plist` file is a standard Apple property list file.  It contains a single array with one entry per word.  In other words

````
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <array>
      word-1
      ...
      word-N
    </array>
</plist>
````

Each word is represented as a dictionary with these keys.

+ `word` (`String`): The word localized in the word list's language.  (In other words, what appears on the screen during the game)  **This is the only required entry in the dictionary.**
+ `key` (`String`): If present, used as the default value for all other string dictionary entries except `sortkey` which always uses the value of `word`.
+ `disabled` (`Bool`): If present, indicates whether or not this word is disabled (i.e., does not appear during the game).  Default is `false`
+ `sortkey` (`String`): If present, use this value when sorting the word list alphabetically for play.  Otherwise, use the value of `word`.
+ `caseOverride` (`Number`): If present, always show this word using the indicated case.  Otherwise, use `Options.shared.letterCase`.  Values are
    + `0`: Uppercase
    + `1`: Lowercase
    + `2`: Capitalized

The following entries are all optional and, as noted above, default to the value of `key` if present or `word` otherwise:

+ `picture` (`String`): The name of the picture file displayed on screen for this word.
+ `speech` (`String`): The name of the speech (nee, spoken word) file for this word.
+ `sound` (`String`): The name of the sound file that is played during the reward sequence (e.g., barking for a dog).
+ `background` (`String`): The name of the background image file displayed for this word.

Here is an example word from the `Animals_ja` word list

````
<dict>
  <key>word</key>
  <string>あり</string>
  <key>key</key>
  <string>ant</string>
  <key>background</key>
  <string>bg_litegrass</string>
</dict>
````

---


## Creating a New FirstWords app

*TODO: TO BE SUPPLIED*
