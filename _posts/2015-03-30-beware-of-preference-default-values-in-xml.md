---
layout: post
title: Beware of Preference Default Values in XML
post_author: cygery
---

Various [Preferences](http://developer.android.com/reference/android/preference/Preference.html) available in Android 
make the maintanance of settings straightforward. Even custom Preferences, e.g., for colors, can be created without much effort. 
By defining [PreferenceScreens](http://developer.android.com/reference/android/preference/PreferenceScreen.html) via XML, 
default values for each Preference can be set. However, parsing and type conversions of these default values can 
lead to unexpected and undesired results.

### Android Preferences

Using a [PreferenceActivity](http://developer.android.com/reference/android/preference/PreferenceActivity.html)/[PreferenceFragment](http://developer.android.com/reference/android/preference/PreferenceFragment.html) and 
a [PreferenceScreen](http://developer.android.com/reference/android/preference/PreferenceScreen.html), setup via XML, 
[Preferences](http://developer.android.com/reference/android/preference/Preference.html) can be easily maintained and 
presented to the user.

#### Example

The following code snippet shows an example PreferenceScreen with an 
[EditTextPreference](http://developer.android.com/reference/android/preference/EditTextPreference.html) 
(e.g., saved as `res/xml/prefs.xml`):

{% highlight xml %}
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

    <EditTextPreference
        android:key="pref_key_edit_text"
        android:title="EditText"/>

</PreferenceScreen>
{% endhighlight %}

The EditTextPreference has the key `pref_key_edit_text` and the title `EditText`.

In a PreferenceActivity, this PreferenceScreen can be loaded using the `addPreferencesFromResource` method:

{% highlight java %}
public class SettingsActivity extends PreferenceActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        addPreferencesFromResource(R.xml.prefs);
    }
}
{% endhighlight %}

The stored Preference values can be accessed via 
[SharedPreferences](http://developer.android.com/reference/android/content/SharedPreferences.html).

#### Preference Types and Underlying Types

Different Preferences use different types for their values. For example, 
[EditTextPreference](http://developer.android.com/reference/android/preference/EditTextPreference.html) uses String, 
[CheckBoxPreference](http://developer.android.com/reference/android/preference/CheckBoxPreference.html) boolean, 
and [MultiSelectListPreference](http://developer.android.com/reference/android/preference/MultiSelectListPreference.html) 
a set of Strings.

#### Default Values

A default value for a Preference can be set using the `android:defaultValue` attribute:

{% highlight xml %}
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

    <EditTextPreference
        android:key="pref_key_edit_text"
        android:title="EditText"
        android:defaultValue="Default value"/>

</PreferenceScreen>
{% endhighlight %}

Alternatively, a reference to a string can be used.

### The Problem

Although EditTextPreference stores its value as a String, a default value set in the XML code isn't simply handled as a String. 
Instead, during build, default values are parsed, e.g., as floats. This can have unexpected and, usually, undesired effects. 
Example:

{% highlight xml %}
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

    <EditTextPreference
        android:key="pref_key_edit_text"
        android:title="EditText"
        android:defaultValue="inf"/>

</PreferenceScreen>
{% endhighlight %}

The default value `inf` is parsed as a float, converted to a String, and then stored as the Preference default value. 
This results in a String value of `Infinity` being stored instead of `inf`.

Default values which can't be parsed as a number are not affected.

#### Examples

##### Values Parsed as Float

| Value set in XML | Resulting String value |
|------------------|------------------------|
| inf              | Infinity               |
| -inf             | -Infinity              |
| nan              | NaN                    |
| +1               | 1.0                    |
| 0.33333333       | 0.33333334             |
| 340282356779700000000000000000000000000. | 3.4028235E38 |

##### Values Parsed as Int

| Value set in XML | Resulting String value | Comment |
|------------------|------------------------|---------|
| 2147483647 | 2147483647 | max. int value |
| 2147483648 | -2147483648 | overflow (max. int value + 1) |
| 4294967296 | 0 | overflow (two times max. int value) |
| 340282356779700000000000000000000000000 | 872415232 | overflow |
| 01 | 1 | leading zeroes are removed |

#### Bug Reports

There [exist](https://code.google.com/p/android/issues/detail?id=75693) 
[multiple](https://code.google.com/p/android/issues/detail?id=74279) 
[bug reports](https://code.google.com/p/android/issues/detail?id=12005) 
about this issue

### Solution

Simple: Use a reference to a string for the default value:

{% highlight xml %}
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

    <EditTextPreference
        android:key="pref_key_edit_text"
        android:title="EditText"
        android:defaultValue="@string/my_default_string"/>

</PreferenceScreen>
{% endhighlight %}

The official [guide](http://developer.android.com/guide/topics/ui/settings.html#Defaults) also uses 
references for String values in the examples. However, it does not mention what might happen when 
default values (like `inf`) are set directly, without using a String reference.
