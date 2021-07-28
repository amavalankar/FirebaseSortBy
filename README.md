# How to Sort Through Items in a Firebase Realtime Database
A brief solution to today's lab — (CSSI 102, Day #13)

## Introduction
As seen in today's _oddly spicy_ mild task, sorting through items in a Firebase JSON object using the provided functions is rather difficult! However, it's not impossible!

**The question of the hour is:** given the following data structure...
```json
{
  "users" : {
    "sMcCXBsDInQ00uuLXENRczHciZm2" : {
      "-MfiucEDJg2k5Z_K5Ddc" : {
        "text" : "This is where text would go. Isn't technology great?",
        "title" : "This is a great title, but alphabetically, it comes last!"
      },
      "-Mfivsk4YQXtqM5C651q" : {
        "text" : "More text? You've gotta be kidding me!",
        "title" : "1 title that should come first in the sorted list."
      },
      "-Mfj-u6YOmFtAR17X_EW" : {
        "bgColor" : "6d2ada",
        "doTextContrast" : false,
        "text" : "Lorem ipsum dolor sit amet. Were you expecting some quirky response, here?",
        "title" : "A wonderful title that, alphabetically, comes after numeric values."
      }
    }
  }
}
```
#### **... how can we sort by the title, in ascending order**?

Specificially, how can we do so using the snazzy functions Google has embedded into their Firebase Realtime Database, like `.orderByChild()`?

## Solution
The solution to our issue relies on 
