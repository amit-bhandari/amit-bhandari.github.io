+++
title = 'Extend Image View to allow URL binding in xml directly'
author = 'Amit Bhandari'
date = 2018-04-10T12:00:18+05:30
draft = false
tags = ['Android', 'Image View', 'Data Binding']
description = 'How to extend image view to allow url binding through XML'
+++

If you are using data binding to bind java pojo to view in Android, you must have came across the issue that you can’t really bind image url in xml directly. Android Image View does not support it by default. But you can easily extend Android Image View to create your own custom view which can support it.

Lets get started.

First of all, to allow your views to pick parameter from xml, resource needs to be declared in attrs.xml in in values resources. Go ahead and create attrs.xml in values directory if it doesn't exists already and add this one parameter in it.

{{< highlight xml >}}
<declare-styleable name="MyImageView">
    <attr name="url" format="string" />
</declare-styleable>

{{< /highlight >}}


Now create class MyImageView and extend it from ImageView. Example code below in kotlin, but java counterpart will be pretty much same.

{{< highlight kotlin >}}

class MyImageView : ImageView {
    var url: String = ""
        set(value){
            field = value
            Glide.with(this).load(value).into(this)
        }

    constructor(context: Context) : super(context)

    constructor(context: Context, attrs : AttributeSet) : super(context,attrs){
        val array = context.obtainStyledAttributes(attrs, R.styleable.MyImageView)
        val url = array.getString(R.styleable.MyImageView_url)
        if(url!=null){
            Glide.with(this).load(url).into(this)
        }
        array.recycle()
    }

    constructor(context: Context,  attrs: AttributeSet , defStyleAttr : Int) : super(context, attrs, defStyleAttr)
}

{{< /highlight >}}

As noticed in constructor, we are picking up url from xml if provided and setting it as source using Glide. You can use any third party library you want for that matter.

Now you can use our created custom image view in xml like this.

{{< highlight xml >}}
<our.package.path.MyImageView
    android:id="@+id/image_view"
    android:layout_width="100dp"
    android:layout_height="100dp"
    app:url="https://www.famouslogos.net/images/android-logo.jpg"/>
{{< /highlight >}}


As you can see how easily we can now set url directly from xml for our image view. But hardcoding url like this is not useful at all and we can use data binding to bind variable directly to url of image view.

If you have no idea what data binding is, I suggest you to go through this and this. It is awesome by the way.

Now our xml looks like this where pojo is your pojo variable name.

{{< highlight xml >}}
<our.package.path.MyImageView
    android:id="@+id/image_view"
    android:layout_width="100dp"
    android:layout_height="100dp"
    app:url="@{pojo.invoiceLogoLink}"/>
{{< /highlight >}}


If you are wondering how we manage the scenario where this parameter is changes at runtime, you can see setter for url variable like this in kotlin class,

{{< highlight kotlin >}}
var url: String = ""
    set(value){
        field = value
        Glide.with(this).load(value).into(this)
    }
{{< /highlight >}}

Every time value changes in pojo, setter is invoked and we change the image using Glide.

Clap if you like what you read.

---

This article was originally written on medium [here](https://medium.com/@amit-bhandari/extend-image-view-to-allow-url-binding-in-xml-directly-c6d9211f717d)

---