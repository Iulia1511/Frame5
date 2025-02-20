# Лабораторная работа № 5
## Цель
* Познакомиться с основами компонентов безопасности в Laravel, таких как аутентификация, авторизация, защита от CSRF, а также использование встроенных механизмов для управления доступом. Освоить подходы к безопасной разработке, включая создание защищенных маршрутов и управление ролями пользователей.
## №2. Аутентификация пользователей
* Создайте контроллер AuthController для управления аутентификацией пользователей.
``` php artisan make:controller AuthController ```
* Добавьте и реализуйте методы для регистрации, входа и выхода пользователя.
#### Обработка данных входа

![image](https://github.com/user-attachments/assets/2bef0299-804e-4a86-a921-3b7481456370)

#### Выход

![image](https://github.com/user-attachments/assets/2983800a-5852-49b3-868d-2445f6b6b547)

#### Регистрация

![image](https://github.com/user-attachments/assets/8c26a834-6d4a-4f5c-a47c-d4397cabbea4)

* Создаем маршруты для регистрации, входа и выхода пользователя.
``` use App\Http\Controllers\AuthController; ```

![image](https://github.com/user-attachments/assets/f7be3615-efc8-4551-b25f-69c6a8eb951d)

![image](https://github.com/user-attachments/assets/19edd930-3999-41db-8601-c0472fa8994a)

![image](https://github.com/user-attachments/assets/be0e422a-5782-4402-8397-daba4fc10b00)

* Создаем отдельный класс Request для валидации данных при регистрации или входе.

```php
      public function storeRegister(Request $request)
{
    $validated = $request->validate([
        'name' => 'required|string|min:3|max:50',
        'email' => 'required|string|email|max:255|unique:users,email',
        'password' => 'required|string|min:6|confirmed',
    ]);

    User::create([
        'name' => $validated['name'],
        'email' => $validated['email'],
        'password' => Hash::make($validated['password']),
    ]);
    public function storeLogin(Request $request)
    {
        $request->validate([
            'email' => 'required|email',
            'password' => 'required|string',
        ]);}}
```
* Установливаем библиотеку Laravel Breeze для быстрой настройки аутентификации. 
- `composer require laravel/breeze --dev`
* Реализуем страницу "Личный кабинет", доступ к которой имеют только авторизованные пользователи.

```php
<!-- resources/views/profile/index.blade.php -->
@extends('layouts.app')

@section('content')
    <div class="container">
        <h1>Личный кабинет</h1>
        <p>Добро пожаловать, {{ $user->name }}!</p>
        <p>Email: {{ $user->email }}</p>

        <a href="{{ route('logout') }}" onclick="event.preventDefault(); document.getElementById('logout-form').submit();">
            Выйти
        </a>

        <form id="logout-form" action="{{ route('logout') }}" method="POST" style="display: none;">
            @csrf
        </form>
    </div>
@endsection
```

* Настраиваем проверку доступа к данной странице, добавив middleware auth в маршрут.

```php
//cabinet
Route::middleware(['auth'])->get('/dashboard', [ProfileController::class, 'index'])->name('dashboard');
Route::middleware('auth')->group(function () {
Route::get('/profile', [ProfileController::class, 'index'])->name('profile.index');
// Для администраторов
Route::middleware('admin')->get('/admin/users', [AdminController::class, 'index'])->name('admin.users');});
```
# Контрольные вопросы
* Какие готовые решения для аутентификации предоставляет Laravel?
* Laravel предоставляет встроенную систему аутентификации с помощью laravel/ui (с Bootstrap) или laravel/breeze, а также более продвинутые решения, такие как Laravel Jetstream и Laravel Sanctum для API-аутентификации.

* Какие методы аутентификации пользователей вы знаете?
* Основные методы аутентификации включают аутентификацию по паролю, через OAuth (Google, Facebook, GitHub), токен-based (JWT, Sanctum), двухфакторную аутентификацию (2FA) и биометрическую аутентификацию.

* Чем отличается аутентификация от авторизации?
* Аутентификация — это процесс проверки личности пользователя (логин и пароль), а авторизация — это проверка прав доступа к определенным ресурсам или функциям системы.

* Как обеспечить защиту от CSRF-атак в Laravel?
* Laravel автоматически защищает от CSRF-атак, проверяя CSRF-токен во всех POST, PUT, PATCH и DELETE-запросах; для этого в форму необходимо добавить директиву @csrf.
