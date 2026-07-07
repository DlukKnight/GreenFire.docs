# **ListWidgetComponent**

## **Description**

Displays a collection of data delivered in rows through a list format. It supports a name, description, image, badge and hyperlink per row.

List widgets are highly effective for displaying sequential information, actionable tasks, or real-time event streams at a glance. They allow users to quickly scan items without the visual density of a traditional table.

Some examples to use list widgets include:

* **Last created records**: Displaying the most recently created records for an entity, for example, the last 5 registered users, with their avatar, a badge indicating if they have confirmed their email and an hyperlink to their profile.
* **Rankings**: Showing a list of items per ranking, like the last 5 biggest sales, for example.
* **To-do lists**: Very good at showing list of tasks to be done, since they are pretty much displayed as notifications.
* **Logs**: Naturally fit for logs taking into consideration their notification-like structure.

List widgets have their own definition in the backend through a [**List Widget Provider**](../../definitions/providers/widgets/listwidget.md), which can be identified by their **code**.

This widget inherits all properties from **BaseWidget**. The metadata below only documents the properties specific to **ListWidget**.

## **ListWidget metadata**

=== "React"

```ts
interface ListWidget extends BaseWidget {
  title: string;
  showImages: boolean;
};
```

### title

Text title of the widget.

### showImages

Shows or hides the image in each row.

## Example

=== "ASP.NET"

```csharp
row.AddWidgetToRow(
  new ListWidget(typeof(UsersListWidget), "Last registered users") 
  { 
      UpdateTimeInSeconds = 5,
      ShowImages = true
  });
```
