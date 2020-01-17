# learning-wont-stop
Many pieces of tips, tricks or just knowledge that I have picked up along the way while using Laravel.

### factory()->raw();

When using factories and you want to return an array without persisting, simply use the `raw()` method. You can as well override any attributes. See example below:

```php
$post = factory(App\Post::calss)->raw(['title' => 'My first post']);
```

### Use multiple responses to make better feature tests

You can do something like:

```php
public function test_can_create_a_post()
{
  $attributes = [
    'title' => $this->faker->sentence,
    'description' => $this->faker->paragraph
  ];
	
  // first response
  $this->post('/posts', $attributes)
    ->assertRedirect('/posts');

  $this->assertDatabaseHas('posts', $attributes);
  // second response
  $this->get('/posts')
    ->assertSee($attributes['title']);
}
```

### request()->validate() returns array of validated items

So, instead of:

```php
request()->validate(['title', 'description']);
//then
$attributes = request()->only('title', 'description');
```

You can do all that in one line as:

```php
$attributes = request()->validate(['title', 'description']);
```

