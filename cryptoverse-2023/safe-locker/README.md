# safe-locker

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f879062a-35e6-4566-8cd7-5dafc5444eeb)

Excerpt from index.html:

```js
Module().then(function(mod) {
  const password_checker = mod.cwrap('checker', 'boolean', ['string']);

  if (password_checker(comboArray.join(""), comboArray.length)) {
    $('.lock-dial ul').draggable('disable');
    $('#lock-wrapper').addClass("unlocked");
    $('.lock-dial').each(function(){
      var $this = $(this);
      $this.find('ul').delay(400).css('color', '#0f0').fadeOut(function(){
        $this.animate({
          marginTop: 150
        }, function(){
          $this.fadeOut(function() {
            $('.is-solved').fadeIn();
          });
        });
      });
    });
  }
});
```

## solution

I didn't want to decompile the web assembly.

Here, we brute force password_checker because there are only 100,000,000 options total, so we can do this pretty easily.

I found the combo to be `86094317`, so the flag is `cvctf{86094317}`

To save memory, I did 10,000,000 combos at a time and changed the last digit manually in my bruteforce for index.html. Then, I refreshed the page with the next digit.
