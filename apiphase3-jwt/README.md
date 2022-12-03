PHASE #3 API DEVELOPMENT IN LARAVRL 8 USING JWT AUTH

https://onlinewebtutorblog.com/rest-api-development-in-laravel-8-with-jwt-authentication/

1.
2.
3.
4.
5.
6.
7.

JWT STRUCTURE : header.payload.signature

# JWT INSTALLATION
    composer require tymon/jwt-auth
    php artisan migrate
# Update config/app.php
    'providers' => [
       ...
        Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
       ...
    ],
    'aliases' => [
       ...
       'JWTAuth' => Tymon\JWTAuth\Facades\JWTAuth::class,
       'JWTFactory' => Tymon\JWTAuth\Facades\JWTFactory::class,
       ...
    ],
# JWT Configure Publish
    php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
# Generate JWT Secret
    php artisan jwt:secret
    When we run this command, it will generate secret key and add into .env file. It will be something like this –
    JWT_SECRET=<JWT-SECRET-KEY>
# Update config/auth.php
    'defaults' => [
      'guard' => 'api', // update here web to api
      'passwords' => 'users',
    ],
    'guards' => [
      'web' => [
        'driver' => 'session',
        'provider' => 'users',
      ],
      'api' => [ 
        'driver' => 'jwt',// update here token to jwt
        'provider' => 'users',
        'hash' => false,
      ],
    ],

# Update Model/User
<?php

namespace App\Models;

use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;

use Tymon\JWTAuth\Contracts\JWTSubject; // Add this line

class User extends Authenticatable implements JWTSubject // Added here
{
    use HasFactory, Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name',
        'email',
        'password',
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password',
        'remember_token',
    ];

    /**
     * The attributes that should be cast to native types.
     *
     * @var array
     */
    protected $casts = [
        'email_verified_at' => 'datetime',
    ];

  	// Add this method
    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    // Add this method
    /**
     * Return a key value array, containing any custom claims to be added to the JWT.
     *
     * @return array
     */
    public function getJWTCustomClaims()
    {
        return [];
    }
}
