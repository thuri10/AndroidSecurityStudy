
<!— @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} —>

<!— code_chunk_output —>

* [FRIDA Script Series (3) Hypnotism: Baidu AI "Tuning" Douyin AI] (#frida Script Series 3 Hypnotism: Baidu ai Tuning Douyin ai)
* [0x01. Japanese TikTok Beauty Paintings] (#0x01 - Japanese TikTok Beauty Paintings)
* [0x02. How to Make TikTok AI Smarter] (#0x02- How to Make TikTok AI Smarter)
* [Like & Follow & Slide & Forward: Analog Tap] (#Like & Forward analog tap)
* [Comment: Impersonate Tap & Input Box] (#Comment Impersonate Tap Input Box)
* [0x03. How to "train" TikTok AI] (#0x03- How to train TikTokAi)
* [screenshot] (#screenshot)
* [Return picture to 'PC'] (#Return picture to pc)
* [Upload images to Baidu'AI' platform] (#Upload images to Baidu'ai platform)
* [Get Baidu'AI' results] (#Get Baidu ai results)
* [Like it or not according to the results] (#Like it or not according to the results)
* [0x04. Baidu AI Effects Demo] (#0x04-Baidu ai Effects Demo)

<!— /code_chunk_output —>



## FRIDA Script Series (3) Superhuman: Baidu AI "Tuning" Douyin AI

### 0x01. Japanese TikTok

In the vile capitalist world, where girls are generally underfed and have little to do every day, they spend their days basking in the sun on the beach, shooting short videos to fill their empty souls, and dressing in such a way that the fabric is generally not too much, and sometimes even ** a few threads ** is used in place of the fabric, capitalism is so abhorrent.

![](pic/01.gif)

![](pic/02.gif)

![](pic/03.gif)

![](pic/04.gif)

To prepare my socialist heirs and bring more people with me to criticize capitalism, I have circulated some of these videos among my friends ([tiktok], and I have collected enough 'mp4' samples to be critical enough.)

Many people said that they needed such negative textbooks at this moment, some even sent personal messages asking me how to get more of them. They have successfully entered enemy strongholds through a series of methods, so much to the chagrin of those who don't understand Japanese and have painted some tasteless videos.

To address your rigid needs and build a community of programmers that are strong, democratic, culturally civilized and harmonious, let's examine how the cutting-edge 'AI' technology, combined with the glue frame—'frida', can be used to increase your chances of painting negative textbooks that will truly benefit programmers and increase your sense of well being and accessibility.

(PS: This article uses the black production customary technology in large quantities, in order to avoid imitating, only introduces the outdated tools, and does not do the debugging)

### 0x02. How to Make TikTok AI Smarter

First of all, let's look at the interface of Douyin, Japan, which is not very different from the domestic version.

![](pic/05.png)

It's easy to think of all the things you love about the negative, especially the swimsuits, the beach, the bikinis, and just the crazy likes.

In full, it's like likes, concerns, comments and retweets, and these are positive feedback.

Or even swim to a favorite Love Beans page and give every video she posts a thumbs-up, a positive feedback that's big enough but blurs our goal — the swimsuit bikini, which isn't every short video anyway.

We should aim for a bikini! Otherwise, what's the point of infiltrating the enemy? The homegrown sisters are charming enough.

What if it's not a bikini? Negative feedback seems to be really low, just slide up and skip the video.

#### Like & Follow & Slide & Forward: Analog Tap

There are too many analog click technology on the Android phone, the common key wizard, touch wizard, accessibility, adb debug instructions directly click and so on.

This industrial chain is also very mature, a large number of black and gray practitioners developed a large number of script tools to achieve the "custom" function.

![](pic/07.jpg)

Once 'frida-server' has been injected into the target process, a library of mock clicks can be loaded to specifically like, follow, slide up, and forward the specified content according to the 'frida' script and the logic of the click script. The positions of these operations are fixed, and can be realized as long as the analog user clicks on them.

![](pic/08.jpg)

Accessibility is another method, and it's a completely legal method, after all, it's a feature of Android system, which is provided to people with disabilities, 'frida-server' injected into the target process, you can load some accessibility code base directly to operate.

In addition, Android's debug command 'adb' can also be clicked directly on the screen, in the following format. 'adb'-related commands can be invoked directly from the 'os' library of 'frida' on the client side of 'python' for screen-clicking related operations, 'adb'-clicking related research is particularly numerous, and Baidu can search for a lot of code.

```bash
adb shell
shell@PRO6:/ $ input tap 125 521
shell@PRO6:/ $
```

The most 'low' way is to run in the 'Windows' simulator, with the 'Intel Houdini for arm-x86', or run Douyin up, but Douyin will definitely be tested. The 'pywin32' of 'python' is then used to implement the click of the 'Windows' window, which has two advantages, one is that it is out of touch with the real machine, the cost is greatly reduced, and the other is that it is easy to do batch quantization. Disadvantages are also easily detected.

Of course, such needs are also common on 'iOS' and, in addition to the touch wizards themselves supporting the 'iOS' end, there is an endless stream of 'iOS' analog click libraries, and 'frida-server' can load these 'gadget' and invoke 'APIs' for analog click, double click, slide and other functions.

![](pic/09.jpg)

#### Comment: Analog Tap & Input Box

The function of analog clicking is realized, the input of comments in the input box is more small dish, using 'frida-server' to 'hook' input box is just like playing, it is a start skill, here is a typical 'hook' 'TextView' code.

```js
    console.log("Script loaded successfully ");
    Java.perform(function(){
    var tv_class = Java.use("android.widget.TextView");
    tv_class.setText.overload("java.lang.CharSequence").implementation = function(x){
    var string_to_send = x.toString();
    var string_to_recv;
    send(string_to_send); // send data to python code
    recv(function(received_json_object){
    string_to_recv = received_json_object.my_data
    }).wait(); //block execution till the message is received
    return this.setText(string_to_recv);
    }
});
```

It is important to note that the input strings cannot all be the same, which is too obvious, and it is best to save a 'lick dog set .db' with 'sqlite' and then keep pulling it from the inside.

### 0x03. How to "train" TikTok AI

#### Screenshot

On the client side of 'frida', using the 'python' tune 'os' package, execute the 'adb' debug command to capture a running mobile phone, which is saved on the 'sd' card. The frequency of running can be every two or three seconds, etc., depending on the specific debugging results.

```bash
$ adb shell screencap -p /sdcard/01.png
```

#### Return pictures to 'PC'

The logic is the same as the screenshot. Retrieve the 'sd' card photo.

```bash
$ adb pull /sdcard/01.png
```

#### Upload image to Baidu'AI' platform

The 'AI' platform of Baidu is still relatively easy to use and provides a wide range of functions such as 'GIF' pornographic recognition, short video review, image moderation, etc. After the pictures are encoded using 'base64', 'post' to Baidu'AI' servers can be used, and the 'json' data returned is the result of Baidu'AI' determination of the pictures.

![](pic/10.JPG)


#### Get Baidu'AI' results

The returned 'json' package is also simple and crude, printing out non-compliant content and possibilities directly, such formatted data would be too easy to extract for logical judgment.

```json
{
    "conclusion": "Non-compliant",
    "log_id": "15537510677705536",
    "data": [
    {
    "msg": "sexy content",
    "probability": 0.999525,
    "type": 2
    },
    {
    "msg": "Watermark Content Present",
    "probability": 0.8558467,
    "type": 5
    },
    {
    "msg": "public figure exists",
    "stars": [
    {
    "probability": 0.73777246,
    "name": "Sun Shu-may"
    }
    ],
    "type": 11
    }
    ],
    "conclusionType": 2
}
```

####Decide whether to like it or not based on the results

In the 'frida' client, it is very easy to dynamically affect the results to 'frida-server', which is already well covered in the [previous tutorial](https://github.com/r0ysue/AndroidSecurityStudy).

### 0x04. Baidu AI Effects Demo

If Baidu's 'AI' doesn't exactly circle the 'negative material' we want to see, with all that said, it would be a waste of time. So Baidu's 'AI' must be accurate, and the screening of "negative material" should be unsparing, as far as possible. Although even if there is, it makes sense to use Baidu's 'AI' to play TikTok 'AI' because the success rate is already high.

![](pic/06.png)

We've chosen the Porn Recognition feature to verify what the recognition rate is.

![](pic/11.JPG)

Porn recognition. There's sexy content. Good.

![](pic/12.JPG)

Porn recognition. There's sexy content. Good.

![](pic/13.JPG)

Porn recognition. There's sexy content. Good.

![](pic/14.JPG)

Porn recognition. Pass. Good.

![](pic/16.JPG)

Porn recognition. Pass. Good.

![](pic/15.JPG)

Porn recognition. There's sexy content. Good.

Baidu'AI' is awesome! The bikini is not falling! It'll all come out!

That's great.

Anthracene, TikTok, you're almost there!
