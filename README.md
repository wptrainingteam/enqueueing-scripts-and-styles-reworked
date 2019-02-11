# Enqueueing Scripts And Styles (Reworked)

## Description

WordPress relies on several programming languages to provide a solid, yet extensible, framework to build upon. Client-side languages like HTML, CSS, JavaScript and jQuery produce a rich and seemingly limitless user experience. The server-side language PHP does the heavy lifting behind the scenes to provide data to the browser. At its core, WordPress already manages a long list of scripts and styles to produce a functioning web page. Many scripts and styles are included in WordPress core; others are added by plugins or themes. WordPress has built-in systems to keep track of all the scripts and styles, keep them from conflicting with each other, and use them in the correct order. The method for adding your own scripts and styles to your theme is called _enqueueing_. Enqueueing is used for both scripts and styles, and means we must access and alter the functions.php file that comes with your WordPress installation. Don’t worry about being fluent in PHP programming; the information you need for this lesson is provided. Additional and deeper PHP learning is beyond the lesson’s scope so we encourage you to look at other resources such as [the official PHP manual](http://php.net/manual/en/intro-whatis.php). For this lesson, the goal is to simply become familiar with enqueueing so you can complete it in its most basic form. _Note: The method for adding scripts and styles to WordPress is essentially the same for both plugins and themes. In this lesson, we will be customizing a theme, but what you will learn is also applicable to building plugins._

## Objectives

At the end of this lesson, you will be able to:

*   Tap into WordPress’ system for managing myriad scripts and styles
*   Correctly include and load your own scripts and styles such that WordPress manages them efficiently

## Prerequisite Skills

You will be better equipped to work through this lesson if you have experience in and familiarity with:

*   Basic knowledge of HTML/[CSS](https://make.wordpress.org/training/handbook/theme-school/intro-to-css/)
*   Basic knowledge of [installing and activating WordPress themes](https://make.wordpress.org/training/handbook/user-lessons/choosing-and-installing-a-theme/)
*   Understanding of how folders and files are structured
*   Ability to edit files with a text editor

## Assets

*   Installation of WordPress
*   Twenty Seventeen theme
*   Child theme "MyChildTheme" [(download zip file)](https://make.wordpress.org/training/files/2017/05/mychildtheme.zip)

## Screening Questions

*   Are you familiar with installing and activating themes via the WordPress Dashboard?
*   Do you have at least a basic knowledge of HTML/CSS?
*   Do you feel comfortable using a text editor to edit code?
*   Will you have a locally or remotely hosted sandbox WordPress site to use during class?

## Teacher Notes

*   **<del>Time Estimate: </del>**
*   Performing a live demo while teaching the steps is crucial to having the material “click” for students.
*   It is easiest for students to work on a locally installed copy of WordPress. Set some time aside before class to assist students with installing WordPress locally if they need it. For more information on how to install WordPress locally, please visit our [Teacher Resources page](https://make.wordpress.org/training/teacher-resources/).
*   The preferred answers to the screening questions is “yes.” Participants who reply “no” to all 4 questions may not be ready for this lesson.
*   Built using WordPress 4.7.4.

## Hands-on Walkthrough

You will be working with a child theme of the Twenty Seventeen theme. It is considered a best practice to use a child theme, since parent themes will receive periodic updates the theme's author and the updates will overwrite any changes or customizations made directly in a parent theme. Using a child theme allows you to freely experiment without altering the parent theme. For more on this, consult the lesson plan on [building child themes](https://make.wordpress.org/training/handbook/theme-school/child-themes/#why-use-a-child-theme),

### Install and activate the parent and child themes

Log in to your WordPress admin dashboard. In the dashboard menu on the left of your screen, select Appearance, then Themes. If this is a fresh install, the Twenty Seventeen theme should already be activated on the site. If it is not, please install it now by clicking on the plus sign in the box labeled, **_Add New Theme_**. After you've ensured you've got the _**Twenty Seventeen**_ theme loaded, download and install the child theme called MyChildTheme: [Download  the zip file here](https://make.wordpress.org/training/files/2017/05/mychildtheme.zip), or from the [Assets](#assets) list above. Activate the child theme, then view your site and notice how it looks with this theme. Now you will change it and see how that affects the site's appearance. View the files of your WordPress installation, and navigate to `/wp-content/themes/` Notice that there is a folder for the parent theme, `twentyseventeen`, and one for the child theme, `mychildtheme`. Open the child theme folder, find the `functions.php` file, and open it in a text editor. The stylesheet for the parent theme is already set up and enqueued in this child theme, as described in the [Child Themes lesson](https://make.wordpress.org/training/handbook/theme-school/child-themes).

```PHP
<?php 
function mychildtheme_enqueue_styles() {
   wp_enqueue_style( 'parent-style', get_template_directory_uri() . '/style.css' );
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' ); 
```

The function `mychildtheme_enqueue_styles()` activates the parent theme's CSS styles. Specifically, it tells WordPress to use the stylesheet called `style.css`. The `get_template_directory_uri()` part specifies where `style.css` is located: it's in the main folder of the parent theme. In other words, WordPress should load the CSS styles found in `/wp-content/themes/twentyseventeen/style.css.` (Later, you will see how to specify a file in the child theme's directory). Try deactivating the enqueuing statement by adding `//` before it:

```PHP
<?php 
function mychildtheme_enqueue_styles() {
   //wp_enqueue_style( 'parent-style', get_template_directory_uri() . '/style.css' );
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' ); 
```

Now WordPress will not use the styles found in the parent theme's `style.css` file. Save `functions.php`, view your site and refresh the browser. Because of the missing styles, the site should now look all wrong, with incorrect image sizes, font sizes, and text alignment. Remove the `//` from the enqueing statement, save and refresh, and the site should look right again. The `add_action()` statement tells WordPress which function to run: `mychildtheme_enqueue_styles`. It also specifies an [action hook](#): `wp_enqueue_scripts()`. The action hook tells WordPress when to run this function in the long list of functions it runs every time it renders a webpage. Despite its name, this hook is used for enqueuing **both** scripts and styles. If you deactivate the statement that contains `add_action()`, the function `mychildtheme_enqueue_styles()` will still exist, but it will never run. Therefore the parent theme's stylesheet will never be enqueued, and the site will look broken again, just as it did when you deactivated the `wp_enqueue_style()` statement. Try this now:

```PHP
<?php 
function mychildtheme_enqueue_styles() {
   wp_enqueue_style( 'parent-style', get_template_directory_uri() . '/style.css' );
}
//add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' ); 
```

Save `functions.php`, view your site and refresh the browser. Notice the effect this had on the site. Reactivate the function by removing the `//` from your code. Save and refresh, and all should be back to normal.

### Enqueue the child theme's main stylesheet

Your Child Theme’s `style.css` file can be empty. But if it contains CSS code, as it usually will, you will need to enqueue it as well. Start with the `functions.php` code you already examined:

```PHP
<?php 
function mychildtheme_enqueue_styles() {
   wp_enqueue_style( 'parent-style', get_template_directory_uri() . '/style.css' );
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' ); 
```

Now add a statement to enqueue the child theme stylesheet. This is almost the same as enqueing the parent stylesheet. But when you specify the location of this stylesheet, use `get_stylesheet_directory_uri()` instead of `get_template_directory_uri()`. This directs WordPress to look for the stylesheet in the child theme rather than the parent, or in other words: `/wp-content/themes/mychildtheme/style.css.`

```PHP
<?php 
function mychildtheme_enqueue_styles() {
   wp_enqueue_style( 'parent-style', get_template_directory_uri() . '/style.css' );
   wp_enqueue_style( 'child-style', get_stylesheet_directory_uri() . '/style.css');
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' ); 
```

Because the order of CSS rules matter, it's important to ensure that the child theme styles load **after** the parent theme styles. This is achieved by adding the parent styles as a [dependency](#) of the child styles. Instead of naming the parent styles with a simple string, `parent-style`, specify that name with a variable `$parent_style` so it can used in more than one place:

```PHP
<?php 
function mychildtheme_enqueue_styles() {

$parent_style = 'parent-style';
   wp_enqueue_style( $parent_style, get_template_directory_uri() . '/style.css' );
   wp_enqueue_style( 'child-style', get_stylesheet_directory_uri() . '/style.css');
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' ); 
```

Then add `$parent_style` as part of an array, after a comma, in the child styles enqueuing statement:

```PHP
<?php 
function mychildtheme_enqueue_styles() {
$parent_style = 'parent-style';
   wp_enqueue_style( $parent_style, get_template_directory_uri() . '/style.css' );
   wp_enqueue_style( 'child-style', get_stylesheet_directory_uri() . '/style.css', array( $parent_style ));
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' ); 
```

If there were multiple dependencies, there would be other values in the array as well. But since there is only one dependency, `$parent_style` is the only element in the array. To make sure the child theme styles are enqueued as expected, add some CSS to the child theme's `style.css` file. (Note that these CSS examples are not intended to be examples of good web design, but are instead intended to be obvious changes that make it easy to see that the stylesheet is working. Remove them after testing, if you like.)

<pre> .wrap{
     border: thick dotted magenta;
 }
</pre>

Save the file, view and refresh the site, and confirm that the front-end display has been changed by the new CSS rule.

### Add an extra stylesheet

You can put all your custom CSS rules into the child theme's main `style.css` file if you want to. But if you choose to divide your CSS into more than one file, you will need to enqueue each CSS file in `functions.php`. Let's create a new CSS file, add some CSS rules, and enqueue it. In your `mychildtheme` folder, create a new file called `style-extra.css`. Open it in your text editor and add a CSS rule:

<pre> .entry-content{
     border: thick solid lime;
 }
</pre>

If you save and view your site now, you should see no change yet. The new CSS rule is not active, because the `style-extra.css` stylesheet has not yet been enqueued. To enqueue the new stylesheet, add another statement to the `mychildtheme_enqueue_styles()` function in `functions.php`:

```PHP
<?php 
function mychildtheme_enqueue_styles(){
$parent_style = 'parent-style';
   wp_enqueue_style( $parent_style, get_template_directory_uri() . '/style.css' );
   wp_enqueue_style( 'child-style', get_stylesheet_directory_uri() . '/style.css', array( $parent_style )); 
   wp_enqueue_style( 'extra-style', get_stylesheet_directory_uri() . '/style-extra.css', array( $parent_style ));
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' );
```

Now if you save `functions.php` refresh the browser and view the site, you should see a thick lime-green border around elements with class `entry-content`.

### Enqueue a Google Font

Another use for enqueuing styles is adding external resources such as Google Fonts. Here's how to change the main font to "Pacifico". Note that again, this is not intended to be an example of good web design. Because Pacifico looks very different from any default fonts, it will be useful as a test to make sure the enqueuing and activating steps have been done correctly. Add this statement to the `mychildtheme_enqueue_styles()` function: `wp_enqueue_style('mychildtheme_googlefonts', 'https://fonts.googleapis.com/css?family=Pacifico');` The whole code block now looks like this:

```PHP
<?php
function mychildtheme_enqueue_styles(){
$parent_style = 'parent-style';
   wp_enqueue_style( $parent_style, get_template_directory_uri() . '/style.css' );
   wp_enqueue_style( 'child-style', get_stylesheet_directory_uri() . '/style.css', array( $parent_style )); 
   wp_enqueue_style( 'extra-style', get_stylesheet_directory_uri() . '/style-extra.css', array( $parent_style ));
   wp_enqueue_style('mychildtheme_googlefonts', 'https://fonts.googleapis.com/css?family=Pacifico');
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' );
```

Now apply the new font in the `styles.css` file. Or you can add it to `styles-extra.css`. Either one should work.

<pre> body{
     font-family: 'Pacifico';
 }</pre>

Save, refresh, and view the site. If all is working, most of the text is now decorative and almost unreadable. It's best to delete this CSS rule after testing.

### Add custom JavaScript

The process for enqueuing scripts is very similar to enqueuing styles. In your `mychildtheme` folder, create a new file called `scripts.js`. Open it in your text editor and add some simple JavaScript:

<pre>function myTestMessage(){
document.write('

# This text is from scripts.js!

'); } myTestMessage();
</pre>
Now enqueue your new script by adding another statement to the `mychildtheme_enqueue_styles()` function in `functions.php`:

```PHP
<?php 
function mychildtheme_enqueue_styles(){
$parent_style = 'parent-style';
   wp_enqueue_style( $parent_style, get_template_directory_uri() . '/style.css' );
   wp_enqueue_style( 'child-style', get_stylesheet_directory_uri() . '/style.css', array( $parent_style )); 
   wp_enqueue_style( 'extra-style', get_stylesheet_directory_uri() . '/style-extra.css', array( $parent_style ));
   wp_enqueue_script('myscript', get_stylesheet_directory_uri() . '/scripts.js' );
}
add_action( 'wp_enqueue_scripts', 'mychildtheme_enqueue_styles' );
```

This time you used the `wp_enqueue_script()` function instead of `wp_enqueue_style()`. Also, because `scripts.js` is located in your child theme, you used `get_stylesheet_directory_uri()` to specify its location, just as you did with the CSS files. Once you save `functions.php`, your script will be loaded each time WordPress loads a page on the front end of your site. The text "This text is from scripts.js!" should appear at the top of each page.
