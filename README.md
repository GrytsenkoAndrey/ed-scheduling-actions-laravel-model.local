# ed-scheduling-actions-laravel-model.local
Scheduling Actions on Models in Laravel

## Scheduling actions in models in Laravel

We want something to be executing in setted date and time

![image](https://user-images.githubusercontent.com/63291871/224537973-075c8a4b-80ca-46fa-908a-f73e660cb3c0.png)

we have **users** table and we want to delete user at **deletes_at** time.

first of all we have to run

```php artisan short-schedule:run```

(we will implement it soon) and also we have to run

```php artisan queue:work```


## Fresh Laravel project

1) add column
```php artisan make:migration add_deletes_at_to_users_table```

![image](https://user-images.githubusercontent.com/63291871/224538124-7555a51a-32cf-4256-bfce-39dc0d56c599.png)

2) run migration
3) create fake users (can use **tinker**)
4) run ```php artisan make:command DeleteUsers```
5) command **handle()**

```
User::where('deletes_at', '=<', now())->each(fn(User $user) => $user->delete());
```

6) check the command
7) but we want to schedule this: 

7.1) we can go to the ```app/Console/Kernel.php->schedule()```

```
$schedule->command('users:delete')->everyMinute();
```

7.2) but we can also use next [Laravel-short-schedule](https://github.com/spatie/laravel-short-schedule)
![image](https://user-images.githubusercontent.com/63291871/224538460-323e3f8e-e8e7-47d5-a7c2-19929e60ab5d.png)


8) lets improve our code, make Job ```php artisan make:job DeleteUser```
9) into the ```Commands/DeleteUser.php```
![image](https://user-images.githubusercontent.com/63291871/224538601-1409f1ce-748d-4c4c-b008-0e858b9dbbe5.png)

10) use DI to inject User to the DeleteUser job (into the constructor -> ```public User $user```)
11) into the ```Jobs\DeleteUser::handle()``` -> ```$this->user->delete();```
12) generate queje table ```php artisan queue:table```
13) ```php artisan migrate```
14) ```php artisan queue:work```
15) if you have failed job because of already deleted user (we have to add **uniqueId** to the job:
15.1) add to implements ```ShouldBeUnique```

15.2) add method ```uniqueId()```

15.3) into method:

![image](https://user-images.githubusercontent.com/63291871/224539143-52d52fa0-ee16-436a-a9e2-620bedb87492.png)

but we can make like this:
```return 'delete_user_' . $this->user->id;```









