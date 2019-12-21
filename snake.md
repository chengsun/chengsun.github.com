---
layout: post
title: Snake in 849 bytes
---

<button style="font-size: 200%;" onclick='V()'>Play game</button>


Back in 2011 I challenged myself to write a snake game in a bookmarklet in under 1KiB. This was the result.

```javascript
javascript:for(Q=64,m=b=Q*Q,a=[P=l=u=d=p=S=w=0],u=89,f=(h=j=t=(b+Q)/2)-1,(B=(D=document).body).appendChild(x=D.createElement('p')),(X=x.style).position='fixed',X.left=X.top=0,X.background='#FFF',x.innerHTML='<p></p><canvas>',v=(s=x.childNodes)[0],(s=s[1]).width=s.height=Q*5,c=s.getContext('2d'),onkeydown=onblur=F=function(e,z){z?a[f]?(w+=m,f=Math.random(l+=8)*(R=Q-2)*R|(u=0),F(f+=Q+1+(f/R|0)*2,z)):F(f):e<0?(l?--l:(y=t,t=a[t]-2,F(y)),S+=(w*=.8)/4,m=999/(u+++10),a[h+=[-1,-Q,1,Q][d=p]]?B.removeChild(x,alert('Game Over')):(F(h),F(e,j=h),v.innerHTML=P?(setTimeout(F,50,e,0),S|0):'Press P')):-e?(y=(a[e]=e<Q|e>=Q*Q-Q|!(e%Q)|e%Q==Q-1|(e==h)*2)+(e==f),e==h&&(a[j]=2+h),c.fillStyle='hsl('+!a[e]*99+','+m*2+'%,'+y*50+'%)',c.fillRect(e%Q*5,(e/Q|0)*5,5,5)):isNaN(y=e.keyCode-37)|y==43?(P=y&&!P)&&F(-1):p=!P|y&-4|!(y^2^d)?p:y;return!1};--b;F(b));void F(-1)
```

<script>
function V(){
for(Q=64,m=b=Q*Q,a=[P=l=u=d=p=S=w=0],u=89,f=(h=j=t=(b+Q)/2)-1,(B=(D=document).body).appendChild(x=D.createElement('p')),(X=x.style).position='fixed',X.left=X.top=0,X.background='#FFF',x.innerHTML='<p></p><canvas>',v=(s=x.childNodes)[0],(s=s[1]).width=s.height=Q*5,c=s.getContext('2d'),onkeydown=onblur=F=function(e,z){z?a[f]?(w+=m,f=Math.random(l+=8)*(R=Q-2)*R|(u=0),F(f+=Q+1+(f/R|0)*2,z)):F(f):e<0?(l?--l:(y=t,t=a[t]-2,F(y)),S+=(w*=.8)/4,m=999/(u+++10),a[h+=[-1,-Q,1,Q][d=p]]?B.removeChild(x,alert('Game Over')):(F(h),F(e,j=h),v.innerHTML=P?(setTimeout(F,50,e,0),S|0):'Press P')):-e?(y=(a[e]=e<Q|e>=Q*Q-Q|!(e%Q)|e%Q==Q-1|(e==h)*2)+(e==f),e==h&&(a[j]=2+h),c.fillStyle='hsl('+!a[e]*99+','+m*2+'%,'+y*50+'%)',c.fillRect(e%Q*5,(e/Q|0)*5,5,5)):isNaN(y=e.keyCode-37)|y==43?(P=y&&!P)&&F(-1):p=!P|y&-4|!(y^2^d)?p:y;return!1};--b;F(b));void F(-1)
}
</script>

The game features colours, pausing (press P), a ramped scoring system (where the score increases gradually after you reach the food, to encourage staying alive afterwards), and "aging food" (where the longer it takes for you to reach the food, the less score you get from it. The food becomes mouldy over time, as indicated by its colour).

The unminified code follows:

```javascript
javascript:for(
Q=64,/*maze size*/
m=b=Q*Q,
a=[P=l=u=d=p=S=w=0],/*a=grid,P=playing,l=food-iters left (until tail starts moving),u=food age,d=dir,p=prev-dir,S=score,w=score to be added on,m=the score to be added if food was eaten now*/
u=89,
f=(h=j=t=(b+Q)/2)-1,/*f=food,h=head,j=prev-head,t=tail. set food to left so gets picked up immediately, setting initial snake size*/
(B=(D=document).body).appendChild(x=D.createElement('p')),(X=x.style).position='fixed',X.left=X.top=0,X.background='#FFF',x.innerHTML='<p></p><canvas>',
v=(s=x.childNodes)[0],(s=s[1]).width=s.height=Q*5,c=s.getContext('2d'),
onkeydown=onblur=F=function(e,z){
z? /*calc food*/
a[f]?(
w+=m,/*aging food score calc*/
f=Math.random(l+=8)*(R=Q-2)*R|(u=0),F(f+=Q+1+(f/R|0)*2,z)/*random (re)calc food loc*/
):F(f)/*draw food*/
:e<0?( /*iter*/
l?--l:(y=t,t=a[t]-2,F(y)),/*set tail*/
S+=(w*=.8)/4,/*pretty score up*/
m=999/(u+++10),/*age food a bit*/
a[h+=[-1,-Q,1,Q][d=p]]?/*collision detect*/
B.removeChild(x,alert('Game Over')):
(F(h),/*draw head*/
F(e,j=h),/*calc food*/
v.innerHTML=P?(setTimeout(F,50,e,0),S|0):'Press P')/*next iter*/
):-e?(/*draw (+recalc sqr)*/
y=(a[e]=
e<Q|e>=Q*Q-Q|/*tb walls*/
!(e%Q)|e%Q==Q-1|/*lr walls*/
(e==h)*2)/*snake head*/
+(e==f),
e==h&&(a[j]=2+h),/*snake chain*/
c.fillStyle='hsl('+!a[e]*99+','+m*2+'%,'+y*50+'%)',/*color calc*/
c.fillRect(e%Q*5,(e/Q|0)*5,5,5)
):/*keyinput*/
isNaN(y=e.keyCode-37)|y==43?
(P=y&&!P)&&F(-1)/*pause*/
:p=
!P|y&-4|!(y^2^d)?/*arrow keys&&not directly opposite*/
p:y;
return!1
};--b;F(b));/*init grid*/
void F(-1)/*dummy update*/
```
