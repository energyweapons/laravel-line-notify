## Laravel 7.x LINE Notify Provider

LINE Notify Provider for Laravel 7.x Notification.

### Requirements

- PHP >= 7.2.5
- Laravel 7.x (Recommend 7.x)

## Usage

1. install package

    ```
    $ composer require hinaloe/laravel-line-notify
    ```

1. Add `Energyweapons\LineNotify\LineNotifyServiceProvider` to `config/app.php` or like.

1. Make notifable class (ex User Model entity)

    ```php
   <?php
    
   namespace App;
   
   use Illuminate\Notifications\Notifiable;
   use Illuminate\Foundation\Auth\User as Authenticatable;
   
   class User extends Authenticatable
   {
       use Notifiable;
       
       // ...
       
       /**
        * @return string LINE Notify OAuth2 token 
        */
       protected function routeNotificationForLine()
       {
           return 'Put you token here';
       }
   }
    
    ```
    
1. Make notification

    ```php
    <?php
    
    namespace App\Notifications;
    
    use App\User;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Notifications\Notification;
    use Hinaloe\LineNotify\Message\LineMessage;
    
    class NewUserNotification extends Notification// implements ShouldQueue
    {
        use Queueable;
        
        /** @var User  */
        protected $user;
        
        /**
         * Create a new notification instance.
         *
         * @param User $user
         */
        public function __construct(User $user)
        {
            $this->user  = $user;
        }
        
        /**
         * Get the notification's delivery channels.
         *
         * @param  mixed $notifiable
         *
         * @return array
         */
        public function via($notifiable)
        {
            return ['line'];
        }
        
        /**
         * @param mixed $notifable callee instance
         * @return LineMessage 
         */
        public function toLine($notifable)
        {
            return (new LineMessage())->message('New user: ' . $this->user->name)
                ->imageUrl('https://example.com/sample.jpg') // With image url (jpeg only)
                ->imageFile('/path/to/image.png') // With image file (png/jpg/gif will convert to jpg)
                ->sticker(40, 2); // With Sticker
        }
    }
    ```
    
1. call `$notifable->notify()`

    ```php
    $user = User::find(114514);
    $user->notify(new NewUserNotification($user));
    ```
