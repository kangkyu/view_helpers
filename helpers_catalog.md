http://archive.oreilly.com/pub/a/ruby/excerpts/ruby-learning-rails/ruby-catalog-helper-methods.html

##A Catalog of Helper Methods: Appendix D - Learning Rails
by Edd Dumbill, Simon St. Laurent (2008)

Everyone who has used Rails for a while has their own set of “commonly used” helper methods. Many times, though, those commonly used sets are different. Some people use FormHelper for all of their forms, while others prefer FormTagHelper. Some people use AssetTagHelper, while others handcode links to static resources like stylesheets and images.

Rather than provide a comprehensive reference to these methods—the API documentation does that—this appendix provides a catalog you can browse to decide which methods might actually prove useful to your own needs. Much of the difficulty in using helpers is in finding them before you reinvent the wheel yourself.

All of these classes are subclasses of `ActionView::Helpers`.

>###Note
the Rails API documentation http://api.rubyonrails.org/

###Calling Helper Methods

Every helper method has its own set of parameters, and often it’s not clear from the documentation which parameters it will accept. How do you interpret the following?

```
label(object_name, method, text = nil, options = {})
```

The first few parameters at least take simple values. The object_name parameter will take a symbol pointing to an ActiveRecord object, like :person. The method parameter, though—what method does it take? It actually wants a symbol, say :name, for an attribute from the object specified in the previous parameter. Why would the Rails documentation call that a method? Because it’ll use a method to access the attribute.

The next parameter, text, is shown with its default value, nil. Any time you see a parameter listed as equal to something, that value is the default.

And options? What is options? It looks like lots of methods must have the same options, because they all have the same entry in the documentation, but it’s really just a convention. The actual options, named parameters, are listed below in the documentation for the method. Sometimes the options just create HTML attributes—use the name of the attribute to create an attribute, like :id => 'myIDvalue'. Other times the helper methods take more specific options that fit their particular needs. You don’t generally need to surround the options in {}, either.

>###Note

>For more on a case where the curly braces ({}) are necessary, see the section called “Creating Checkboxes” ” in Chapter 6, Presenting Models with Forms.

>There’s also a case—with FormHelper methods in particular—where some of the parameters disappear into a context object. See the section the section called “Form As a Wrapper” ” in Chapter 6, Presenting Models with Forms for more information on how this works.

Sometimes you’ll also see parameters listed that begin with an asterisk, like *sources. This means that you can supply multiple values for that parameter.

Parameters and named parameters are enough for most helper method calls, but every now and then you’ll see a method whose arguments end with &block. form_for is one of the commonly used ones that does this, but some methods take this as an option and others require it. When you call a method with a block, however, the block doesn’t look quite like part of the arguments:

```
<% benchmark "It took this long:" do %>
  <%= my_long_method %>
<% end % >
```

In this case, the benchmark method is taking two arguments. The first, a string, is “It took this long:”; this will be text incorporated in the log. The second argument starts with do and closes with end, and includes everything in the middle. That’s the block. (Blocks can also be marked with { and } in normal Ruby code, but in the ERb where you’ll be writing helper methods, do and end are a better choice.)

Because benchmark is keeping track of how long it takes some code to run, it needs that code included as an argument. The cache, capture, and content_for methods have similar needs, as do form_for and fields_for, which surround a group of methods and provide them context.

For developers coming from less flexible languages, Ruby’s creative use of blocks can be very difficult to figure out. If you’re feeling stuck, your best option is to work from examples until you’re ready to move forward with your own experiments.

###ActiveRecordHelper
The `ActiveRecordHelper` class seems intent on providing the fastest possible path from an ActiveRecord object to an HTML representation. These methods may be useful for putting together very quick demonstrations or for debugging purposes, but they aren’t likely to be your best choice for application-building. (In general, FormHelper and FormTagHelper are better choices for building forms.)

`error_message_on`
Returns a div containing the error message for a given object and method. You can add text before or after the message.

`error_messages_for`
Returns a div containing all the error messages for a given object. (The documentation suggests that you look at the code and make your own method if you need something more specific.)

`form`
Creates a POST-based form based on the ActiveRecord object, all in one call. You can add extra pieces to the form through a block, but mostly this is good for quick-and-dirty instant forms.

`input`
Creates an input element based on the type of the object and method it’s passed. It’s kind of like a field-by-field version of form.

###AssetTagHelper
In Rails terms, an asset is something static that you want to include on a web page that isn’t controlled by Rails. These include things like stylesheets, JavaScript libraries, and sometimes images.

When working on a small scale, assets are stored in the public/ directory of your Rails application, but you can put them on a separate server and tell Rails where to find them through ActionController::Base.asset_host. A separate server can speed delivery, let you share assets with other applications, or just reduce the amount of work your Rails application has to do itself.

The methods in AssetTagHelper will assume files are in your public/ directory unless you’ve specified otherwise. Most of them generate HTML tags for you, though a few let you specify ways to generate tags in the future.

The methods you should probably focus on initially include:

`auto_discovery_link_tag`
Lets you specify the location of an RSS or Atom feed.

`image_tag`
Returns an HTML img tag for the specified image name.

`javascript_include_tag`
Returns a script tag for the JavaScript files you identify as parameters. If one of the parameters is :defaults, the application.js file will be included, bringing in the Prototype and Script.aculo.us libraries. You can provide full paths to your scripts, even scripts on other servers, or you can just provide the file’s name. If you’re feeling fancy, you can define groups of styles with register_javascript_expansion, and reference them with symbols.

`stylesheet_link_tag`
Returns a link tag for the CSS stylesheet files you identify as parameters. You can provide full paths to your stylesheets, even stylesheets on other servers, or you can just provide the name of the file. The :all symbol will link all of the stylesheets in the public/stylesheets directory. As with scripts, if you’re feeling really fancy, you can define groups of styles with register_stylesheet_expansion, and reference them with symbols.

There are other methods in AssetTagHelper, but they’re mostly internal or only used in special cases:

`image_path` (or `path_to_image`)
An internal method used to calculate where to point for an image.

`javascript_path` (or `path_to_javascript`)
An internal method used to calculate where to point for a JavaScript file.

`register_javascript_expansion`
Lets you register a symbol that can reference JavaScript files. Useful if you consistently use a group of script files together.

`register_javascript_include_default`
Lets you add JavaScript files to the :defaults symbol used by the javascript_include_tag method.

`register_stylesheet_expansion`
Lets you register a symbol that can reference style files. Useful if you consistently use a group of stylesheets together.

`stylesheet_path` (or `path_to_stylesheet`)
An internal method used to calculate where to point for a stylesheet.

###AtomFeedHelper and AtomFeedHelper::AtomFeedBuilder
Atom feeds started out as more or less the next generation of RSS syndication feeds. RSS can stand for Really Simple Syndication, Rich Site Summary, or RDF Site Summary, which is part of why Atom’s developers decided to start over with a new name. Syndication feeds make it easy for sites, especially news sites and weblogs, to share their content with other websites and consumers, offering a simpler format for articles than full HTML pages. Atom, especially its REST-based Atom Publishing Protocol (AtomPub), has grown beyond just exchanging lists of articles, but Rails’ built-in helper functions focus on fairly traditional feed applications.

AtomHelper is mostly used in Builder templates for creating XML:

`atom_feed`
Takes a block, creating an atom:feed element and giving the block an AtomFeedBuilder object that child components can use for context. It also accepts parameters for :language, :root_url, :url, and :schema_date.

`entry`
Creates an atom:entry element. Accepts parameters for :published, which represents the time when the entry was first published, :update, which represents the time of the latest changes, and :url, where to find the entry.

`updated`
Takes a time and converts it to the right format for Atom feed times.

###BenchmarkHelper, CacheHelper, and CaptureHelper
All three of these classes contain methods that wrap around content in your templates. BenchmarkHelper is a class you’ll mostly want to use during development, when it may help you isolate code that’s taking the view a long time to run. CacheHelper and CaptureHelper are both for advanced development. While CacheHelper allows you to specify fragments of your views that will be stored for future reuse, and applied when the same call comes through, CaptureHelper lets you manually grab content that needs to be used again in the same view, probably to share content from the template with the layout:

`benchmark`
The benchmark method takes an optional message argument and an optional logging level argument (:debug, :info, :warn, or :error). It records how long the wrapped code takes to run. It requires a block argument, so it usually looks something like:

```
<% benchmark "It took this long:" do %>
 <%= my_long_method %>
<% end % >
```

The message and the length of time it takes to run will end up in the logs.

`cache`
The cache method lets you flag fragments of your view to be kept for caching. Like benchmark, cache wraps around the view code it’s meant to work on with a block argument:

```
<% cache do %>
  <%= my_repetitive_method_that_should_be_cached %>
<% end % >
```

You should only cache information that doesn’t change very often, but many HTML components are pretty stable.

`capture`
The capture method wraps around view code and stores its output to a variable. You can then reference the variable and have that content appear wherever you need. In operation, it looks like a variable assignment to a method:

```
<% @trapped_content = capture do %>
  <%= content_to_put_in_there %>
<% end % >
```

Once you’ve captured it, you can reference @trapped_content wherever it is convenient.

`content_for`
The content_for method is much like capture, but instead of putting the content in a variable, it lets you create a named block you can yield to in order to include the content.

###DateHelper
The DateHelper class contains two kinds of helper methods. There’s a small set of methods for expressing times in somewhat more human-friendly forms:

`distance_of_time_in_words`
Takes two time values and expresses how far apart they are in rough word descriptions rather than precise time notation—e.g., “2 days,” or “about 1 month,” or “less than a minute.”

`distance_of_time_in_words_to_now` or `time_ago_in_words`
Like distance_of_time_in_words, but with the to_time always set to now.

Most of DateHelper’s methods, though, create form fields for specifying times and dates. They’re kind of clunky, but they may be useful for when you’re getting started or when you feel like overriding them. Three of them are bound to particular objects of type :date, :time, or type :datetime:

`date_select`
The date_select method creates drop-down year, month, and day select fields bound to a particular ActiveRecord object of type :date.

`datetime_select`
The datetime_select method creates drop-down year, month, day, hour, minute, and second select fields bound to a particular ActiveRecord object of type :datetime.

`time_select`
The time_select method creates drop-down hour, minute, and second select fields bound to a particular ActiveRecord object of type :time.

The rest of DateHelper’s methods create HTML form fields, but aren’t bound to any particular ActiveRecord object:

`select_date`
The select_date method creates drop-down year, month, and day select fields.

`select_datetime`
The select_datetime method creates drop-down year, month, day, hour, minute, and second select fields.

`select_day`
The select_day method creates a drop-down field for day of the month (1–31).

`select_hour`
The select_hour method creates a drop-down field for hours (0–23).

`select_minute`
The select_minute method creates a drop-down field for minutes (0–59).

`select_month`
The select_month method creates a drop-down field for month (1–12).

`select_second`
The select_second method creates a drop-down field for seconds (0–59).

`select_time`
The select_time method creates drop-down hour, minute, and second select fields.

`select_year`
The select_year method creates a drop-down field for year. By default it uses five years on either side of the current or selected year, but you can set start and end years through parameters.

###DebugHelper
The DebugHelper class isn’t exactly a powerful debugger, but its one method lets you do something that’s often useful in development mode—report an object’s contents:

`debug`
The debug method takes an object as its argument. It then serializes the object into YAML, and wraps the YAML output (and any errors) in pre tags so you can inspect it.

###FormHelper, FormTagHelper, and FormOptionsHelper
These three classes of helper methods offer different approaches to building forms and some different pieces for creating forms. Much of the time you’ll want to use either FormHelper or FormTagHelper, but you might mix FormOptionsHelper with either of the other two.

The FormHelper methods create form fields bound to particular attributes of ActiveRecord objects. They are easiest to use within a form_for method that sets their context, as described in Chapter 6, Presenting Models with Forms. If you don’t like that approach, you can supply an object and attribute name as the first two parameters when calling them. (The documentation for each method shows the parameter approach.)

`check_box`
Creates a checkbox field bound to an attribute from the object specified in form_for or in the parameters. (It also creates a hidden field bound to the same attribute for use if the checkbox isn’t checked.)

`fields_for`
fields_for is like form_for, except that it doesn’t create the actual form tags.

`file_field`
Creates a file upload field bound to an attribute from the object specified in form_for or in the parameters. (You’ll need to modify the form_for call as described in Chapter 8, Improving Forms to use this method.)

`form_for`
Creates a form element and sets the context for the other helper methods in FormHelper.

`hidden_field`
Creates a hidden field bound to an attribute from the object specified in form_for or in the parameters.

`label`
Creates a label for a field created with the other methods of FormHelper.

`password_field`
Creates a password field bound to an attribute from the object specified in form_for or in the parameters.

`radio_button`
Creates a radio button bound to an attribute from the object specified in form_for or in the parameters.

`text_area`
Creates a larger multiline text area bound to an attribute from the object specified in form_for or in the parameters.

`text_field`
Creates a single-line text field bound to an attribute from the object specified in form_for or in the parameters.

The FormTagHelper class does similar work but provides no automatic binding to a single shared object for a form. It lets you build forms where each component is specified separately:

`check_box_tag`
Lets you create checkboxes. Unlike check_box, it doesn’t automatically create a hidden field for use if the box is unchecked.

`field_set_tag`
Creates a fieldset tag for grouping form elements. You can set the legend for the fieldset as an argument.

`file_field_tag`
Creates a file upload tag. To use this, you also need to give the form_tag method a :multipart => true parameter.

`form_tag`
Creates a form tag that wraps around other form elements but does not set context like form_for.

`hidden_field_tag`
Creates a hidden form field.

`image_submit_tag`
Not for submitting images, but rather for creating submit buttons that are presented as images.

`label_tag`
Creates a label.

`password_field_tag`
Creates a password field.

`radio_button_tag`
Creates a radio button.

`select_tag`
Creates a drop-down select or multiselect list.

`submit_tag`
Creates a submit button with a text caption.

`text_area_tag`
Creates a larger multiline text area.

`text_field_tag`
Creates a single-line text field.

The FormOptionsHelper methods are complementary to the methods in the other two form-related helper classes. Some of the methods (`collection_select`, `country_select`, `select`, and `time_zone_select`) take the same arguments as the field-creating methods in FormHelper and can be used the same way, with a context set by `form_for` or without. The other methods are focused on creating options for those methods, and may also be used to create option lists for the FormTagHelper’s select_tag method:

`collection_select`
Creates a select list from a specified object or array.

`country_options_for_select`
Creates option tags for an alphabetical list of countries, accepting arguments to indicate which should be selected and which should appear first in the list.

`country_select`
Creates a complete select list of countries including option tags. Also accepts arguments for a selected default and for giving countries higher priority in the list.

`option_groups_from_collection_for_select`
Creates option tags structured with optgroup tags based on an object or array.

`options_for_select`
Creates option tags based on a hash or array.

`options_from_collection_for_select`
Creates option tags based on a collection object.

`select`
Creates a complete select list including option tags.

`time_zone_options_for_select`
Returns option tags for time zones across the planet.

`time_zone_select`
Returns a complete select list for time zones across the planet.

###JavaScriptHelper
Sometimes your Rails code will need to generate JavaScript, and not always in the context of RJS, as described in Chapter 16, Creating Dynamic Interfaces with Rails and Ajax. These helper methods make it simpler to add basic JavaScript functionality to your pages, and remove the need to code some kinds of simple JavaScript by hand:

`button_to_function`
Creates a button that will call a JavaScript function using its onclick handler. It accepts a block of code, which works like RJS.

`define_javascript_functions`
Creates a link to Prototype and other JavaScript files, but is best avoided. Use javascript_include_tag instead.

`escape_javascript`
Reformats JavaScript containing carriage returns and quotes so that it can safely be put into HTML attribute values.

`javascript_tag`
Creates a script tag. Again, javascript_include_tag may be a better option.

`link_to_function`
Creates a link that will call a JavaScript function. Like button_to_function, it also can take a block that works like RJS.

###NumberHelper
The NumberHelper class provides convenience methods for formatting numbers:

`number_to_currency`
Turns a number into a currency representation. You can select the :unit (denomination), :separator (normally .), :delimiter (normally , ), :format (whether the currency comes before or after the number), and :precision (normally 2).

`number_to_human_size`
Turns file-size byte counts into more typical human representations, like 12 GB.

`number_to_percentage`
Turns a number into a percent value. You can select the :precision (normally three digits after the decimal), and the :separator (normally .).

`number_to_phone`
Turns a number into an American-style telephone number. You can specify a country code, extension, delimiter, and whether or not the area code has parentheses, but you can’t specify how the numbers are broken down.

`number_with_delimiter`
Formats a number with a given :delimiter between thousands (, by default) and decimal :separator (. by default).

`number_with_precision`
Formats a number to present as many digits after the decimal point as are specified in the second argument (three is the default).

###PrototypeHelper
The Prototype JavaScript library simplifies many common Ajax tasks, but these helper methods make it even more convenient to incorporate calls to Prototype in Rails templates:

`evaluate_remote_response`
Evaluates the JavaScript response from a remote service using the JavaScript eval method. The eval method opens JavaScript applications up to attack, so be certain that what you’re processing is free of potentially harmful code.

`form_remote_for`
Same as remote_form_for, described later.

`form_remote_tag`
Creates a form element that uses an XmlHttpRequest call to submit form data. This allows the page to handle the response rather than reloading an entirely new page.

`link_to_remote`
Creates a link that issues an XmlHttpRequest call, again allowing the page to handle the response rather than replacing the current page with a new destination.

`observe_field`
Watches the content of a given field and makes a remote call (or a JavaScript function call) when the content of that field changes. Useful for components like text fields that provide suggestions.

`observe_form`
Watches the content of a given form and makes a remote call (or a JavaScript function call) when the content of that field changes.

`periodically_call_remote`
Makes an XmlHttpRequest call every so often, according to a duration specified in seconds set as the :frequency option.

`remote_form_for`
Works like form_for, except that submitting the form triggers an XmlHttpRequest call that gets handled by Ajax in the browser instead of the usual form submission process.

`remote_function`
Returns the JavaScript needed to make an XmlHttpRequest call to a remote function.

`submit_to_remote`
Creates a button that will submit a form using an XmlHttpRequest call.

`update_page`
Creates the context for RJS calls, allowing a block of code to update multiple elements on the same page.

`update_page_tag`
Creates JavaScript, wrapped in a script tag, using the same mechanisms as creating RJS code.

###RecordIdentificationHelper
These methods make it easier for views to identify components referring to Rails objects when building HTML or RJS:

`dom_class`
Creates a value suitable for a class attribute that is the singular form of the object name.

`dom_id`
Creates a value suitable for an id attribute that is the singular form of the object name plus _ and the object’s id value.

`partial_path`
Creates a value containing plural/singular form of an object’s name, like turtles/turtle or people/person.

###SanitizeHelper
The SanitizeHelper methods support a variety of approaches to escaping HTML and CSS. They complement the h method (short for html_escape, part of the ERb:Util class) by providing other approaches to escaping markup or letting it pass:

`sanitize`
The sanitize method provides a customizable approach to removing attributes and markup that you don’t want to pass through. The customization can be specified through the :tags and :attributes parameters, or set by default through initializer code.

`sanitize_css`
The sanitize_css method removes features from CSS that the creators of sanitize felt were too dangerous. This is used by sanitize on style attributes.

`strip_links`
The strip_links method leaves markup other than links intact, but removes all links from the argument.

`strip_tags`
The strip_tags method removes all HTML markup from the argument. (The documentation warns that it may not always find all HTML markup, however.)

###ScriptaculousHelper
Like the PrototypeHelper methods, ScriptaculousHelper methods provide ready Ruby-based access to JavaScript components in the browser. While Prototype focuses on basic Ajax communications, Script.aculo.us focuses more on actions and special effects:

`draggable_element`
Identifies an HTML element, specified by id attribute value, as supporting user efforts to drag it around the screen and potentially drop it on a receiving element.

`drop_receiving_element`
Identifies an HTML element, again by id attribute value, as a container where users can drop draggable objects and expect a response. When the drop happens, the code this creates can make a remote Ajax call or a local JavaScript call, and supports class-based constraints on which objects to accept. It also supports changing the class of the receiving element to give users visual feedback when they’ve positioned a draggable object over a container that could accept it.

`sortable_element`
Identifies an HTML element, again by id attribute value, as a collection users can reorder, making an Ajax call when changes occur.

`visual_effect`
Provides access to the Script.aculo.us library’s collection of visual effects, applying them to an HTML element specified by id.

###TagHelper
TagHelper may be of use when you want to create explicit XHTML markup using ERb templates, or want to create view logic that lets the data determine which markup is used. On the one hand, these methods are somewhat obscure; on the other, they may be exactly what you need if clean XHTML is your goal:

`cdata_section`
As its name suggests, the cdata_section method lets you create CDATA sections wrapping the content specified in the argument. (CDATA sections let you mark sections of an XML document as not containing any markup, so you can use <, >, and & to your heart’s content.)

`content_tag`
The content_tag method is a generic tag building method. You define the name of the tag, the attributes, and the content of the tag through the arguments.

`escape_once`
The escape_once method is extremely convenient when you have content that needs <, >, and & escaped—but you might already have done some of the escaping. This method is smart enough to escape markup text that needs escaping, while leaving the ampersands that are part of prior escaping alone.

`tag`
The tag method creates an empty tag (like <br />).

###TextHelper
The TextHelper methods offer a variety of tools for formatting, manipulating, and presenting plain text. If you’re building blogs or other software where users are entering content, this class is worth a close look:

`auto_link`
The auto_link method is a simple way to make links live without requiring people to use HTML. Its first argument is a block of text. By default, it will turn all URLs and email addresses in that text into live links. The link parameter defaults to :all, but also accepts :email_addresses and :urls as options if you just want one or the other. The href_options parameter lets you add attributes.

(If you want to get really fancy, you can supply a block of code that will be executed for every link that gets added, letting you control processing precisely.)

`concat`
Used for those obscure times when you want to generate output inside of a <% %> ERb code block instead of the usual <%= %> block.

`cycle`
Creates an object that lets you alternate different values for each member of an array. This lets you do things like alternate formatting to reflect even and odd rows, or mark every 10th element.

`excerpt`
Finds a given phrase in a given text and returns the phrase with surrounding context.

`highlight`
Finds a given phrase in a given text and marks all occurrences with <strong class="highlight" >.

`markdown`
When used with the BlueCloth plug-in (http://www.deveiate.org/projects/BlueCloth), lets you convert text containing Markdown codes (http://daringfireball.net/projects/markdown/syntax) into HTML.

`pluralize`
Lets you apply Rails’ inflector (the same code that manages singular and plural for ActiveRecord objects) to any text you’d like.

`reset_cycle`
Starts a cycle (described earlier) over again.

`simple_format`
Adds HTML line breaks and paragraph marks to plain text.

`textilize`
When used with the RedCloth plug-in (http://whytheluckystiff.net/ruby/redcloth/), lets you convert text containing Textilize codes (http://www.textism.com/tools/textile) into HTML.

`textilize_without_paragraph`
Just like textilize, but with one fewer surrounding paragraph mark `(<p>...</p>)`.

`truncate`
Cuts off the end of a string after a specified number of characters and adds a truncate string, usually ....

`word_wrap`
Wraps text to a specified line width, breaking on whitespace when possible.

###UrlHelper
The UrlHelper class provides methods for creating links inside of your Rails application, letting you take advantage of the routing functionality Rails uses to manage addresses. (And even though the REST world frequently talks about URIs rather than URLs, URLHelper is the place to go to create both.)

`button_to`
Generates a form containing a single button that links to the specified controller. (It’s the same as url_for, but wraps the result in a button.) You can ask users to confirm their interest after clicking the button.

`current_page?`
Returns true if the current page has the same URL as the URL created by the options listed.

`link_to`
Creates a link (a) element linking to the specified controller. (It’s the same as url_for, but wraps the result in an HTML link.)

`link_to_if`
Like link_to, but lets you specify conditions.

`link_to_unless`
Like link_to, but lets you specify prohibitions.

`link_to_unless_current`
Like link_to, but won’t link if the link is to the current page.

`mail_to`
Creates a mailto: link to a given email address.

`url_for`
Creates a URL based on the options provided and the Rails application’s routing table.

If you enjoyed this excerpt, buy a copy of [Learning Rails](http://shop.oreilly.com/product/9780596518783.do).
