# Reviews, The Next Generation

We are returning to your reviews application. *Surprise!*

You can create a new repository or use the same one. If you'd like to use the same one, but would like to keep track of the previous version of your project, you can create a branch.

[Here](./rubric.md) lies the rubric.

## Review Tags

If you didn't include tags before, add them. These should have a many-to-many relationship with reviews.

Clicking on a tag should take the user to a page which displays the reviews associated with that tag (tag details page).

Using appropriate HTML elements and AJAX, allow the user to add custom tags to reviews. 

STRETCH TASK: Be careful not to create a new tag if a tag with that name already exists.

## Review Comments

Add comments to reviews. Comments should be listed on a review's page after the review's content.

Using appropriate HTML elements and AJAX, allow the user to add a comment to a review. Use AJAX to create the comment. After submitting a comment, add the new comment to the list of comments using DOM manipulation.

### Things to ponder

How can we best semantically represent review comments from an HTML perspective?

## Stretch tasks

- When creating a new Tag, don't create duplicates.  Instead, associate the review with the existing tag.
- Use ES6 syntax
- Oh, you've already thought of a few things. Do those, but keep them simple.
