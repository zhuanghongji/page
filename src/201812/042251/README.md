# AsyncTask 的使用与分析

`AsyncTask` 允许对用户界面执行异步操作。它会先阻塞工作线程中的操作，然后在 UI 线程中发布结果，而无需你亲自「处理线程」或「处理程序」。

要使用它，必须创建 `AsyncTask` 的子类并实现 `doInBackground()` 回调方法，该方法将在后台线程池中运行。 要更新 UI，应该实现 `onPostExecute()` 以传递 `doInBackground()` 返回的结果并在 UI 线程中运行，以便你安全地更新 UI。 稍后，你可以通过从 UI 线程调用 `execute()` 来运行任务。

例如，你可以通过以下方式使用 `AsyncTask` 来实现上述示例：

```Java
public void onClick(View v) {
    new DownloadImageTask().execute("http://example.com/image.png");
}

private class DownloadImageTask extends AsyncTask<String, Void, Bitmap> {
    /** The system calls this to perform work in a worker thread and
      * delivers it the parameters given to AsyncTask.execute() */
    protected Bitmap doInBackground(String... urls) {
        return loadImageFromNetwork(urls[0]);
    }

    /** The system calls this to perform work in the UI thread and delivers
      * the result from doInBackground() */
    protected void onPostExecute(Bitmap result) {
        mImageView.setImageBitmap(result);
    }
}
```

现在 UI 是安全的，代码也得到简化，因为任务分解成了两部分：

* 一部分应在工作线程内完成。
* 另一部分应在 UI 线程内完成。

下面简要概述了 `AsyncTask` 的工作方法，但要全面了解如何使用此类，你应阅读 `AsyncTask` 参考文档：

* 可以使用泛型指定参数类型、进度值和任务最终值。
* 方法 `doInBackground()` 会在工作线程上自动执行。
* 方法 `onPreExecute()`、`onPostExecute()` 和 `onProgressUpdate()` 均在 UI 线程中调用。
* `doInBackground()` 返回的值将发送到 `onPostExecute()`。
* 你可以随时在 `doInBackground()` 中调用 `publishProgress()`，以在 UI 线程中执行 `onProgressUpdate()`。
* 你可以随时取消任何线程中的任务。

> 注意：使用工作线程时可能会遇到另一个问题，即：运行时配置变更（例如，用户更改了屏幕方向）导致 `Activity` 意外重启，这可能会销毁工作线程。 要了解如何在这种重启情况下坚持执行任务，以及如何在 `Activity` 被销毁时正确地取消任务，请参阅书架示例应用的源代码。
