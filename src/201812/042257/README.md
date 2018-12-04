# 发起一个标准请求

本文主要描述了如何使用 Volley 提供的通用请求类型：

* `StringRequest`：指定一个 URL 并获取响应中的原生字符串
* `JsonObjectRequest` 和 `JsonArrayRequest`（两者都是 `JsonRequest` 的子类）：指定一个 URL 并获取响应中的 JSON 对象或数组

如果你的请求符合上述类型，你可能就不需要再去自定义请求了。
如何在 Volly 中自定义请求，见下一篇文章「自定义请求」。

## JSON 请求

Volly 提供了下面两个类来处理 JSON 请求：

* `JsonArrayRequest`：用于取出指定 URL 响应 body 的 `JSONArray`
* `JsonObjectRequest`：用于取出指定 URL 响应 body 的 `JSONObject`，允许将 `JSONObject` 作为请求主体的一部分传入

你可以按照与其他请求类型相同的模式去使用上述两种请求。  
例如，下面的代码片段获取一个 JSON feed，然后将其作为文本显示在 UI 中：

```java
TextView mTxtDisplay;
ImageView mImageView;
mTxtDisplay = (TextView) findViewById(R.id.txtDisplay);
String url = "http://my-json-feed";

JsonObjectRequest jsObjRequest = new JsonObjectRequest
        (Request.Method.GET, url, null, new Response.Listener<JSONObject>() {

    @Override
    public void onResponse(JSONObject response) {
        mTxtDisplay.setText("Response: " + response.toString());
    }
}, new Response.ErrorListener() {

    @Override
    public void onErrorResponse(VolleyError error) {
        // TODO Auto-generated method stub

    }
});

// Access the RequestQueue through your singleton class.
MySingleton.getInstance(this).addToRequestQueue(jsObjRequest);
```
