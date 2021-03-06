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
/** @test */
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

### Override the validated method in FormRequest object to add data not submitted by user

`$request->validated()` would normally return the data that was validated in the request. However, you may have data that doesn't need to be validated. This could for example be generated values. So in your validated method of your FormRequest object simply merge in your data like so:

```php
public function validated()
{
    return array_merge(parent::validated(), [
    	// your data here
    ]);
}
```

### Group common assertions to create more meaning in your tests
Take the following test code:

```php
/** @test */
public function guests_cannot_control_project()
{
  $project = factory(\App\Project::class)->create();
  
  $this->get('/projects')->assertRedirect('login');
  $this->post('/projects', $project->toArray())->assertRedirect('login');
  $this->get('/projects/' . $project->id)->assertRedirect('login');
}
```

This condenses what would have been three tests into just one test. 
