# Make Your Own Widgets Easily -Speedy BPM Counter in OSX Dashboard

Web apps are common place, but web apps that do not require internet access are not.

In this article I show you how I made a BPM Counter in a simple HTML page coupled with vanilla javascript ([see here](https://general-use123.s3-eu-west-1.amazonaws.com/bpm.html)). If downloaded, this widget can be used offline — ideal for musicians who want to create but do not always have internet access. Even better, by using the OSX dashboard app (which never seemed that useful before), we can make this BPM Counter extra quick to use.
> # [🔗 Get The BPM Counter Code On Github 📔](https://github.com/sk-t3ch/BPM_Counter)

![](https://cdn-images-1.medium.com/max/2400/0*U10lZ1OTd9RZpRxj.png)

## What should it look like?

Obviously, the answer to question is a matter of opinion. My stance is that it should super simple and only do what a BPM counter needs to: count Beats Per Minute. Therefore, all it needs to be is a button and a count value

![](https://cdn-images-1.medium.com/max/2000/1*Fwxw3vi6b7Qs2MMrWo_EOQ.png)

## The logic

Estimating BPM is as easy as measuring the time between two sequential beats and calculating how many of these you could fit in a minute.

    let prev_click = new Date(); 

    const getBPM = function (){
     const currentTime = new Date();
     const interval = (currentTime - prev_click)/1000;
     const bpm = 60/interval;
     prev_click = currentTime;
     return bpm;
    } 

    get_bpm(); // e.g. 120

I took this further by averaging the 3 previous beats like this:

    const averaging = 3;
    const prev_bpms = [60];

    const getBPM = function() {
     const currentTime = new Date();
     const interval = (currentTime - prev_click) / 1000;
     const bpm = 60 / interval;
     prev_click = currentTime;
     while (prev_bpms.length > prev_bpm_list_max_length) {
        prev_bpms.shift();
     }
     prev_bpms.push(bpm);
     average_bpm = prev_bpms.reduce((acc, cVal) => acc + cVal) / prev_bpms.length;
     return bpm;
    } 
    
    get_bpm(); // e.g. 120

Also, not everyone wants to press the button but maybe instead a key:

    // space bar trigger
    window.addEventListener('keypress', (e) => {
     if(e.code === 32) getBPM();
    });
    // instant ability
    document.querySelector('.clicker button').focus();

Now, users can also tap using the space bar as soon as the page has loaded.

## Hear your BPM

You’ve tapped in your BPM, but now you want to play it back so you can jam along to your favourite tempo.

To do this, we must make sound. But how? We have two options built into the browser [Audio](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) API, use a sound file or create a synthesiser.

We’ll first use the synthesiser to create a beep:

    const AudioContext = window.AudioContext || window.webkitAudioContext;

    let context, oscillator;
    const bpm = 60;
    const bpmInterval = 60/bpm * 1000; //ms

    setInterval(beep, bpmInterval);

    const beep = function (){
          if(!context) context = new AudioContext();
          oscillator = context.createOscillator();
          oscillator.type = "sine";
          oscillator.start(0);
          oscillator.connect(context.destination);
          setTimeout(oscillator.disconnect, 150, context.destination);
        }

Now let’s do a similar thing using an Audio file instead:

    const click = new Audio('./cowbell.mp3');
    const bpm = 60;
    const bpmInterval = 60/bpm * 1000; //ms

    setInterval(beep, bpmInterval);

    const beep = function (){
          click.play();
          setTimeout(()=>{ 
              click.pause(); 
              click.currentTime = 0.0;
            }, 150);
        };

Finally adding the logic which controls them:

    // HTML
    <div class="player">
            <button class="controler" onclick="togglePlayerOutput()">
              &#9654;
            </button>
    </div>
    

    // JS
    let isPlayerPlaying = false;
    let bpmRepeaterId;

    const togglePlayerOutput = function (){
            const button = document.querySelector('.player button');
            if (!isPlayerPlaying){
              button.innerHTML = '&#9724;';
              bpmRepeaterId = setInterval(beep, bpmInterval);
            }
            else{
              button.innerHTML = '&#9654;';
              clearInterval(bpmRepeaterId);
            }
            isPlayerPlaying = !isPlayerPlaying;
        };

## Putting it all together

![](https://cdn-images-1.medium.com/max/2000/1*dqL2RCN9LeI9DV0KGUstoA.png)

Now putting all of the features together and adding a little bit of styling (which I am not going to explain), we have this final product:

I don’t know how much code people really want to see directly in the article, so find the full code at [Github](https://github.com/sk-t3ch/BPM_Counter) .

## Effective Use (OSX Users Only)

If you’ve used a mac before, you might have stumbled across the native Dashboard App, but you probably won’t have stayed long.

I’ve never really used it…until now.

In Safari you can right click on the page, which sometimes causes an action selection to pop up including *open in dashboard…*

![](https://cdn-images-1.medium.com/max/2056/1*0HBoMjMJGgw5wdYAl6AXDg.png)

Clicking on this will reveal to you a web page widget creator. You can select part of the page that you would like to add into your dashboard. This is a pretty cool feature, but for our case, it’s a super cool feature.

Opening the BPM counter we just made, you can select the box like this:

![](https://cdn-images-1.medium.com/max/2800/1*C3xxvy9tDqPTcb2aRTJ8Xg.png)

Now use the shortcut of the F12 key. BOOM. It’s never been that easy to create widgets yourself, quickly and easily.

![](https://cdn-images-1.medium.com/max/2800/1*2GcGa7Qx7TcAQTKBow9eXg.png)

You may be wondering why this one does not include the metronome playback feature. When I attempted to use it in the dashboard, the program would not reliably play out the audio :( But at least Logic can easily do that part.

And the reason why I showed you how to create sounds in two different ways is because the `Audio Context` version using a synthesiser would not work inside the dashboard.

Finally, you can’t simply click F12 and proceed to use the space bar to get the tempo, you must click the button directly, which is a downgrade.

But I think this may be how I make little widgets from now on. If you have any cool ideas for this, show me when you’ve implemented them :)
> # [🔗 Get The BPM Counter Code On Github 📔](https://github.com/sk-t3ch/BPM_Counter)

## Thanks for reading

Check out [T3chFlicks.org](https://t3chflicks.org/Projects/bpm-counter) for more tech-focused educational content ([YouTube](https://www.youtube.com/channel/UC0eSD-tdiJMI5GQTkMmZ-6w), [Instagram](https://www.instagram.com/t3chflicks/), [Facebook](https://www.facebook.com/t3chflicks), [Twitter](https://twitter.com/t3chflicks)).