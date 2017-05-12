# Association

This field allows to select and reorder multiple post type **posts**, **taxonomy terms**, **users** or **comments**. Useful for creating links between any of these items.

### Config methods

`set_types( $types )`

It allows you to specify the types of data that you want to have available in this association field.

In this context:

* `type` is the data type – `post`, `term`, `user`, `comment`, or a custom value (if implemented).
* `subtype` clarifies the exact type to be used. 
	* For the `post_type` `type`, you can use `post`, `page` or any other registered custom post type. 
	* For the `taxonomy` `type`, you can use `category`, `post_tag` or any other registered custom taxonomy. 
	* The `subtype` is not used for the `user` and `comment` types.

Defaults to `array( array( 'type' => 'post', 'post_type' => 'post' ) )`

`set_max( $max )`

Allows you to limit the maximum number of selected items in a association field. By default, there is no limit.

```php
Field::make( 'association', 'crb_association' )
	->set_max( 5 )
```

`allow_duplicates( $allow )`

If enabled will allow the same item to be selected more than once. By default, duplicates are not allowed.

```php
Field::make( 'association', 'crb_association' )
	->allow_duplicates( true )
```

##### NB! To get all the association data (each entry containing `id`, `type`, `subtype`) array, you can set the association type in the retrieval functions. Example:

```php
/* Get the association data as an array */
carbon_get_post_meta( $id, $name );
/*
The above example returns: 
array( 
	0 => array(
		'object_id' => 20,
		'type' => 'post',
		'subtype' => 'page',
		'value' => 'post:page:20',
	), 
	1 => array(
		'object_id' => 16,
		'type' => 'term',
		'subtype' => 'category',
		'value' => 'term:category:16',
	)
)
*/
```

### Example

The example below registers the following options for the association field:

* Post Type: Page
* Post Type: Post
* Taxonomy: Category
* Taxonomy: Post Tag
* Users
* Comments

```php
Field::make( 'association', 'crb_association' )
	->set_types( array(
		array(
			'type' => 'post',
			'post_type' => 'page',
		),
		array(
			'type' => 'post',
			'post_type' => 'post',
		),
		array(
			'type' => 'term',
			'taxonomy' => 'category',
		),
		array(
			'type' => 'term',
			'taxonomy' => 'post_tag',
		),
		array(
			'type' => 'user',
		),
		array(
			'type' => 'comment',
		),
	) )
```
