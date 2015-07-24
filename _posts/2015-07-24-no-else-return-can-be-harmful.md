---
layout: post
title: no-else-return can be harmful
date:   2015-07-23 19:25:28
categories: javascript eslint
---

### Consider:

{% highlight js %}

function elseAfterReturn(someCondition, otherCondition){
    if(someCondition){
        console.log('if');
    } else if (otherCondition){
        console.log('else if');
        return;
    } else{
        // eslint complains no else after return
        console.log('else');
    }
    // do something after if or else, but not else if
    return;
}

function noElseAfterReturn(someCondition, otherCondition){
    if(someCondition){
        console.log('if');
    } else if (otherCondition){
        console.log('else if');
        return;
    }
    console.log('else');
    // do something after if or the supposedly implicit else, but not else if
    return;
}

console.log('else after return');
elseAfterReturn(true, false);

console.log();

console.log('no else after return');
noElseAfterReturn(true, false);
{% endhighlight %}

The output from the two functions is not the same, but don't take my word for it.

<button id="run-script">Run the script!</button>

<pre><code id="script-output"></code></pre>

*I modified the script slightly to print to this code block instead of the console*

eslint might lead you to believe that the two functions above are identical, but they clearly are not.

Obviously the above is very contrived, but this actually bit me while using expressjs.  I had a situation where I accidentally called `next()` more than once in some cases which resulted in erratic and difficult to track down errors.

This probably could be solved with better coding practices, but I think it's worth realizing that `no-else-return` can introduce unexpected errors.

<script>
(function() {
    var output = {
        element: document.getElementById('script-output'),
        log: function(text){
            output.element.innerHTML += text + '\n';
        }
    };  
    function elseAfterReturn(someCondition, otherCondition){
        if(someCondition){
            output.log('if');
        } else if (otherCondition){
            output.log('else if');
            return;
        } else{
            // eslint complains no else after return
            output.log('else');
        }
        // do something after if or else, but not else if
        return;
    }

    function noElseAfterReturn(someCondition, otherCondition){
        if(someCondition){
            output.log('if');
        } else if (otherCondition){
            output.log('else if');
            return;
        }
        output.log('else');
        // do something after if or the supposedly implicit else, but not else if
        return;
    }


    document.getElementById('run-script').addEventListener('click', function(){
        output.log('else after return');
        elseAfterReturn(true, false);

        output.log('');

        output.log('no else after return');
        noElseAfterReturn(true, false);
    });
}());

</script>
