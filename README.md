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
1. First, we simply access the data like we normally do, but instead, add our sorting parameter before the `.on(...)` statement:
```js
const getNotes = (userId) => {

  const notesRef = firebase.database().ref(`users/${userId}`);

  notesRef.on('value', (snapshot) => {
    const data = snapshot.val();
    renderDataAsHtml(data);
  });
};
```

This, like we've previously done, creates a `snapshot` JSON-like object that contains the notes given a specified `uid`. However, the new `snapshot` has a special `forEach(...)` property that will allow us to iterate over the items in the object, filtered by a desired child element. In this case, the child element we're sorting by is the `title` property.

---

2. Now, we iterate over the loop. We call the `renderDataAsHTML()` function which does the job of rendering the HTML.

### Code
<details>
  <summary>Completed Solution</summary>
  
  ```javascript
  const getNotes = (userId) => {
  
    const notesRef = firebase.database().ref(`users/${userId}`);
  
    // Do a similar .on(...) retrieval of data, but this time, sort the data using the .orderByChild(...) method.
    notesRef.orderByChild("title").on('value', snapshot => {
        writeNotesToHTML(snapshot); 
    })
}
  
  const renderDataAsHTML = (data) => {

    let cards = ``;

    data.forEach((child) => {
        // Get note and noteKey from the child
        const note = child.val();
        const noteKey = child.key;
  
        // For each note create an HTML card
        cards += createCard(note, noteKey);
    })
  
    // Inject our string of HTML into our viewNotes.html page
    document.querySelector('#app').innerHTML = cards;
}
  
  const createCard = (note, noteId) => {
    // Here is where we'd actually create the HTML element/text that gets rendered...
    // But that's not the point of this write-up! :)
  }
  ```
</details>

<details>
  <summary>Previous/Old Solution</summary>
  
  ```javascript
  const getNotes = (userId) => {
  
    const notesRef = firebase.database().ref(`users/${userId}`);
  
    notesRef.on('value', (snapshot) => {
      const data = snapshot.val();
      renderDataAsHtml(data);
    });
};

const renderDataAsHtml = (data) => {
  
  let cards = ``;
  
  for(const noteItem in data) {
  
    const note = data[noteItem];
    // For each note create an HTML card
    cards += createCard(note, noteItem)
  
  };
  
  // Inject our string of HTML into our viewNotes.html page
  document.querySelector('#app').innerHTML = cards;
};
  
  const createCard = (note, noteId) => {
    // Here is where we'd actually create the HTML element/text that gets rendered...
    // But that's not the point of this write-up! :)
  }
  ```
</details>

---

### Explanation & Elaboration
Comparing the code we can observe a few key differences:
- First, we retrieve the data _slightly_ differently.
  - We add `.orderByChild("title")` to the retrieval promise. What this does, according to Google's Firebase documentation, is:
  - > Order results by the value of a specified child key or nested child path.

