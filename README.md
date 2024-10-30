# DEV | OP
#### Video Demo:  https://www.youtube.com/watch?v=dQw4w9WgXcQ&ab
## Description:
Dev | OP is a blog made with [Symfony](https://symfony.com/) containing articles about web developpement and dev related project management.

As a visitor you can read the article and create an account.

With a non admin account you can comment the articles and like them which adds them to your list of liked articles.

With an admin account you get access to an admin menu allowing you to CRUD articles, tags, users and comments (comments need to be validated by an admin before being visible).

### Important files:
#### Controllers:
- `AdminController.php` : Contains a single route to the admin menu.
- `RegistrationController.php` : Registration route responsible for the creation of a new User instance on form submission. Makes use of [Symfony's password hasher](https://symfony.com/doc/6.4/security/passwords.html#hashing-the-password) for the user's password and signs in the user automatically on submit.
- `SecurityController.php` : Contains the login and logout routes. This logic is imported from the `AppAuthenticator.php` file which contains the `AppAuthenticator` class and relies on Symfony's [Passport and Badge](https://symfony.com/doc/6.4/security/custom_authenticator.html) functionnalities. 
- `UserController.php` : Contains the CRUD logic for the User entity. Theses route are admin only.
- `BlogController.php` : Contains the CRUD and the like system for the articles. The `app_blog` route has different ways to return the articles depending on the value of the URL parameter. The requests corresponding to these values are imported from the `ArticleRepository.php` file where they are defined.
- `TagController.php` : Contains the CRUD for tags. Tags are linked to article and an article can have 0 to N tags.
- `CommentController.php` : Contains the logic behind listing all the comments (for admin validation), deleting a comment and posting a comment. Validation and deletion only take POST requests.

#### Javascript:
- `articles.js` : This file is used in the menu where admins can manage articles and uses Jquery AJAX. It's role is to detect clicks on the delete buttons and send POSTS requests to the `app_article_delete` contained in the `BlogController.php` file. It sends the id of the article as well as a CSRF token generated in the Twig template to ensure that the request doesn't come from someone trying to forge a request using an actual admin credentials. If the controller succeeded in deleting the article it then sends back a JSON message. The `showToast` function then pushes an HTML toast containing that message into the DOM for user feedback and deletes it after 5 seconds.
- `comments.js` : The logic is pretty much the same as in the `articles.js` file. The code is detecting clicks on the validation and deletion buttons and sends a request accordingly to either `app_comment_delete` or `app_comment_validate`. These routes then send back a JSON response with a message that the JS file displays in a toast.
- `like.js` : Detects clicks on the article's like buttons and sends POST requests to the `like_article` route in the `BlogController.php` file. This route sends back the new number of likes of the article as well as a boolean to help the JS file know if the user has added or removed his like from the article. The JS then updates the DOM by displaying the new counter and changing the look of the like icon for user feedback.
- `myDatatables.js` : The admin menu is using [datatables](https://datatables.net/) to display and manage data. This very short file is only used to initialize these datatables.
- `toast-fade.js` : This very short file gives a slide animation to the toasts and makes them disappears after 5 seconds. The toasts it is impâcting are specifically the ones generated by [Symfony's flash message component](https://symfony.com/doc/6.4/controller.html#managing-the-session).

#### Other:
- `security.yaml` : This configuration file allows us to easily manage the access control of the application. I configured it so that all the routes that start with `/admin` require the `ROLE_ADMIN` role.
- `style.css` : The main CSS file for this application. It uses CSS's variable system and sets the general look of all the components. It also imports fonts from a Google font CDN which should be switched for local fonts in a production environnement.
- `new-article.html.twig` : This Twig file is what admins use to create new articles. It imports [Quill JS](https://quilljs.com/) which allows the admin to use a more advanced text editor for the content of the articles. The "real" text field generated by Symfony is actually hidden and the Quill one is displayed. The JS code in the template listens for the submission of the form and copies the content of the Quill field into the Symfony one. The text generated by Quill contains HTML tags, but displaying any HTML tag on pages exposes websites to XSS attacks. To deal with that problem i used Symfony's [HTML Sanitizer](https://symfony.com/doc/6.4/html_sanitizer.html), it is essentially a Twig filter that uses PHP's [`strip_tags`](https://www.php.net/manual/en/function.strip-tags.php) function to remove any potentially dangerous tags like `<script>` or `<iframe>`.

## Install:
Make sure [git](https://git-scm.com/downloads), [Symfony CLI](https://symfony.com/download) and [Composer](https://getcomposer.org/) are installed.
1. Clone the [project](https://github.com/Phearal/dev_op) with the command `git clone https://github.com/Phearal/dev_op.git`.
2. Open a terminal on project root and run `composer install`.
3. Create a .env file with a link to a MySQL database `DATABASE_URL="mysql://user:password@127.0.0.1:3306/dev_op?serverVersion=8.0.32&charset=utf8mb4"`
4. Run `symfony console doctrine:database:create`
5. If you wish to use my data fixtures (more on fixtures [here](https://symfony.com/bundles/DoctrineFixturesBundle/current/index.html)), run `symfony console doctrine:fixtures:load`
