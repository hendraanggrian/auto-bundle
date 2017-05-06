Bundler
=======
Passing key-value pairs with Bundle is the most common approach of exchanging
data across Android components.
Unfortunately, much of the process of sending and receiving those key-value
pairs (known as extra) requires a lot of boilerplate code.
Bundler aims to minify the process with annotation processing.

```groovy
dependencies {
    compile 'com.hendraanggrian:bundler:0.2.3'
    annotationProcessor 'com.hendraanggrian:bundler-compiler:0.2.3'
}
```

Usage
-----
### Extra binding
`@BindExtra` for binding extra value to field, field cannot be private.
When key is not provided, field name will be used as the key.
`@WrapExtras` are optional, as described below.
```java
@WrapExtras
public class ExampleActivity extends Activity {
    @BindExtra String username;
    @BindExtra int age;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Bundler.bind(this);
        // TODO: Use fields...
    }
}
```

### Extras Wrapping
`@WrapExtras` for creating Bundle with with single `Bundler.wrap()` call.
```java
Intent intent = new Intent(context, ExampleActivity.class);
intent.putExtras(Bundler.wrap(ExampleActivity.class, "Hendra Anggrian", 24));
startActivity(intent);
```

Supported extra types
---------------------
Bundler supports extra types initially supported by Bundle, plus [Parceler][1]:
 * primitive data types and array of them.
 * `CharSequence`, `CharSequence[]`, and `ArrayList<CharSequence>`
 * `String`, `String[]`, and `ArrayList<String>`
 * `Parcelable`, `Parcelable[]`, `ArrayList<Parcelable>`,
   and `SparseArray<Parcelable>`
 * `Serializable`
 * object of any class annotated with `@Parcel`

[Parceler][1] is included in this library, it is a library that easily makes any
object implements Parcelable with generated code, making it able to be inserted
to Bundle as Parcelable.
To use, annotate class with `@Parcel`.
```java
@Parcel
public class User {
    String name;
    int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

`Bundler.bind()` automatically converts the Parcelable back to original object.
```java
@WrapExtras
public class UserActivity extends Activity {
    @BindExtra("user") User user;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Bundler.bind(this);
    }
}
```

`Bundler.wrap()` automatically converts the object to Parcelable.
Without `Bundler.wrap()`, object must be wrapped using `Parcels.wrap()`.
Head to [Parceler doc][1] for more information.
```java
User user = new User("Hendra Anggrian", 24);
// with Bundler.wrap()
intent.putExtras(Bundler.wrap(UserActivity.class, user));
// without Bundler.wrap()
intent.putExtra("user", Parcels.wrap(user));
```

Optional bindings
-----------------
Extra bindings are required by default, an exception will be thrown if key is
not found in Bundle.
If this is not a desired behavior, annotate the field with `@Nullable` from
[Android's support design library][2].
```java
@Nullable @BindExtra String username;
```

License
-------
    Copyright 2017 Hendra Anggrian

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.


 [1]: https://github.com/johncarl81/parceler
 [2]: http://tools.android.com/tech-docs/support-annotations