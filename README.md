# Sorting Through Items in a Firebase Realtime Database 🔥 
A brief solution to today's lab — (CSSI 102, Day #13) 💡 

## Introduction 👇🏽
As seen in today's _oddly spicy 🌶_ mild task, sorting through items in a Firebase JSON object using the provided functions is rather difficult! However, it's not impossible! 👍 

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
#### **... how can we sort by the title, in ascending order**? ⬆️ 

Specificially, how can we do so using the snazzy functions Google has embedded into their Firebase Realtime Database, like `.orderByChild()`?

## Solution 💡 
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

### Code 💻 
<details>
  <summary>🔎 Completed Solution</summary>
  
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
  <summary>📓 Previous/Old Solution</summary>
  
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

### Explanation & Elaboration 👀
Comparing the code we can observe a few key differences:
- First, we retrieve the data _slightly_ differently.
  - We add `.orderByChild("title")` to the retrieval promise. What this does, according to [Google's Firebase documentation (1)](https://firebase.google.com/docs/database/web/lists-of-data#sorting_and_filtering_data), is:
    > Order results by the value of a specified child key or nested child path.
  - We go to the `users > userID > notes` layer, and then the function call `.orderByChild("title")` goes into the note's child, and sorts by title.
    -  The order of sorting (first to last) goes as follows: `null > false > true > numbers (0, 1, ...) > letters/unicode (a, b, ...) > objects (sorted by key names)`
- Second, and most importantly, we iterate through the newly sequenced data using a `forEach(...)` loop.
  - Initially, this is what stumped me, but a glance at the SDK's documentation can guide us towards the light! 
  - When we pass the sorted data object to the `renderDataAsHTML()` function, we are passing a `snapshot` object, which you can read about [here (2)](https://firebase.google.com/docs/reference/node/firebase.database.DataSnapshot).
  - As per the documentation linked above, the passed `snapshot` object, which is referred to within the `renderDataAsHTML()` function as `data`, has a few key attributes which will be useful:
    - The `key` attribute (`data.key`) returns the location/index of the object. This is important as we use this for CRUD operations. This is equivalent to ` noteItem` in the old, unsorted code.
    - The `forEach(...)` method is how we access the data with the specified order, as mentioned previously. The [documentation provided by Google says it best (3)](https://firebase.google.com/docs/reference/node/firebase.database.DataSnapshot#foreach):
      > `forEach()` Enumerates the top-level children in the DataSnapshot.
      
      > Because of the way JavaScript objects work, the ordering of data in the JavaScript object returned by `val()` is not guaranteed to match the ordering on the server nor the ordering of `child_added` events. That is where `forEach()` comes in handy. It guarantees the children of a `DataSnapshot` will be iterated in their query order.
      
      > If no explicit `orderBy*()` method is used, results are returned ordered by key (unless priorities are used, in which case, results are returned by priority). 

## Different Sorting & Filtering Techniques 🔀
Using these techniques is the ideal way to sort and filter data in Firebase. Of course, we can sort and filter data in several different ways. This section will simply summarize the documentation outlined by Google, with some extra elaboration as needed.

### Sorting Methods 📶
| Method         | Usage                                                                     |
|----------------|---------------------------------------------------------------------------|
| `orderByChild()` | Order results by the value of a specified child key or nested child path. |
| `orderByKey()`   | Order results by child keys.                                              |
| `orderByValue()` | Order results by child values.                                            |

**Note: You can only use one sorting method at a time. Otherwise, you'll yield an error!**

### Filtering Methods *️⃣
| Method         	| Usage                                                                                                      	|
|----------------	|------------------------------------------------------------------------------------------------------------	|
| `limitToFirst()` 	| Sets the maximum number of items to return from the beginning of the ordered list of results.              	|
| `limitToLast()`  	| Sets the maximum number of items to return from the end of the ordered list of results.                    	|
| `startAt()`      	| Return items greater than or equal to the specified key or value, depending on the order-by method chosen. 	|
| `startAfter()`   	| Return items greater than the specified key or value depending on the order-by method chosen.              	|
| `endAt()`        	| Return items less than or equal to the specified key or value, depending on the order-by method chosen.    	|
| `endBefore()`    	| Return items less than the specified key or value depending on the order-by method chosen.                 	|
| `equalTo()`      	| Return items equal to the specified key or value, depending on the order-by method chosen.                 	|

**Note: You can use multiple filters and combine them with one sorting method.**

### Other Considerations ✅
Filtering and sorting will require some creativity. For example, you could sort the title alphabetically using the `orderByChild("title")` method, and then call `limitToFirst(50)` to retrieve the first 50 cards whose titles alphabetically by title

You can use the `limitToFirst(n)` and `limitToLast(n)` method calls without a sort-by function, but the other filters seem to rely (logically, at least), on a sorting function.
