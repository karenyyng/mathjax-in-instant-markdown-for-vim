[ref](https://github.com/suan/vim-instant-markdown/issues/67)
In my OSX, the vim-instant-preview is installed in
`/opt/local/lib/node_modules/instant-markdown-d/` folder, only need to change
index.html file

Add MathJax javascript library: add
```html
<script type="text/javascript"
src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>                                                                         
  <script type="text/x-mathjax-config">                                         
	    MathJax.Hub.Config({                                                        
			      tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}                            
						    });                                                                            
								  </script>
```
inside `<head></head>`, then you should be able to see Math
symbols rendered the first time you preview a .md file.
However when you change the file and update the preview you
will find Math symbols not be rendered anymore as mentioned
here, by looking at instant-markdown-d file, you will find
it actually creates aMarkdownIt instance (dependency:
markdown-it) named md, then in the

```html
input.on('end', function(){
    output.emit('newContent', md.render(body));

});
```
it sends the md.render(body) as the injected html back to
the clients, because markdown-it doesn't support rendering
Math yet, that's why the math symbols are gone after
updating the preview. So you 
need to render the math again after the client side
receives the html rendered by markdown-it.

To do this, change relevant part in your index.html to be
like this
```html
socket.on('newContent', function(newHTML) {                                  
        document.querySelector(".markdown-body").innerHTML
				= newHTML;              
				        MathJax.Hub.Queue(["Typeset", MathJax.Hub]);                                                
																		      });
```
After these two steps, it should be
able to display math.
