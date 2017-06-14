# Theme Options

Theme option containers are used to add pages with options in the back-end. Field data is stored as [options](http://codex.wordpress.org/Option_Reference).

By default, theme options containers automatically create main page in the admin area menu named **“Theme Options”**. In most cases these default settings are sufficient, but if you need to change the title or the location of a page in the menu, read the **“Multiple option pages”** section below.

```php
use Carbon_Fields\Container;
use Carbon_Fields\Field;

Container::make('theme_options', 'Theme Options')
	->add_fields(array(
		Field::make('text', 'crb_facebook_url'),
		Field::make('textarea', 'crb_footer_text')
	));
```

### Multiple option pages

It is sometimes needed to create more than one option page. At other times you need to place different pages in different sections in the admin menu. For example, you might have extensive list of settings for the background that you would want to place on a separate Theme options page under Appearance.

To change the location of your Theme Options page, you use `set_page_parent($parent)`, where `$parent` is either:

* Title of a top level Theme Options page.
* Indentificator of a top level menu section in the admin menu sidebar. This corresponds to the `$parent_slug` parameter of [add_submenu_page()](http://codex.wordpress.org/Function_Reference/add_submenu_page). You can see all predefined page parents [here](http://codex.wordpress.org/Function_Reference/add_submenu_page#Parameters).

Below you see sample code for creating three theme option containers:

```php
use Carbon_Fields\Container;
use Carbon_Fields\Field;

// Default options page
Container::make('theme_options', 'Basic Options')
	->add_fields(array(
		Field::make('header_scripts', 'crb_header_script'),
		Field::make('footer_scripts', 'crb_footer_script'),
	));

// Add second options page under 'Basic Options'
Container::make('theme_options', 'Social Links')
	->set_page_parent('Basic Options')	// title of a top level Theme Options page
	->add_fields(array(
		Field::make('text', 'crb_facebook_link'),
		Field::make('text', 'crb_twitter_link')
	));

// Add third options page under "Appearance"
Container::make('theme_options', 'Customize Background')
	->set_page_parent('themes.php')	// identificator of the "Appearance" admin section
	->add_fields(array(
		Field::make('color', 'crb_background_color'),
		Field::make('image', 'crb_background_image')
	));
```

For detailed information on managing admin pages, see [Administration_Menus](http://codex.wordpress.org/Administration_Menus).

Every theme options container requires a level of permission, which by default is set to `edit_themes` (read more about permissions: [Roles & Capabilities](http://codex.wordpress.org/Roles_and_Capabilities)).

You can change the permission required to view your options page using `set_page_permissions($permission)`

### Localizing the page title

By default, the URL of the option page is generated from the title passed to the `Container::make` method. In the example above, the URL will be `admin.php?page=theme-options`. If you use a `gettext` function to localize the title, for example:

```php
Container::make( 'theme_options', __( 'Theme Options', 'my-textdomain' ) )
```

then the URL of your page will be different for different languages. This will cause issues with multilingual plugins, such as WPGlobus or WPML, that allow for admin language switching.

To resolve this, you need to call the `set_page_file` method, like this:

```php
use Carbon_Fields\Container;
use Carbon_Fields\Field;

Container::make( 'theme_options', __( 'Theme Options', 'my-textdomain' ) )
	->set_page_file( 'theme-options' )
	->add_fields(array(
		Field::make('text', 'crb_facebook_url'),
		Field::make('textarea', 'crb_footer_text')
	));
```

### Custom menu icon

To change the icon of your Theme Options page, you use `set_icon($icon)`, where `$icon` can be one of the values, supported in the `$icon_url` parameter of the [add_menu_page()](http://codex.wordpress.org/Function_Reference/add_menu_page) function.

Below you see an example for setting up the icon of the theme options page:

```php
use Carbon_Fields\Container;
use Carbon_Fields\Field;

Container::make('theme_options', 'Basic Options')
	->set_icon('dashicons-carrot')
	->add_fields(array(
		Field::make('text', 'crb_test_field'),
	));
```

##### NB! You can specify custom icons only to parent theme options pages.

### Accessing field values

To retrieve field values from a theme options container, you need to use the function `carbon_get_theme_option($name, $type = null)`, where:

| Parameter            | Description                                                                         |
| -------------------- | ----------------------------------------------------------------------------------- |
| `$name`              | The name of the field to be retrieved.                                              |
| `$type` *(optional)* | If the field you want to retrieve is of type complex, you need to pass `"complex"`. |

```php
<p>Copyright <?php echo carbon_get_theme_option('crb_copyright'); ?></p>
<p>
	Office locations:
	<?php 
	$address_lines = carbon_get_theme_option('crb_addresses', 'complex');
	foreach ($address_lines as $line) {
		echo $line . '<br/>';
	}
	?>
<p>
```

After saving, the `carbon_after_save_theme_options` hook is called, which allows you to hook additional functionality after saving.
