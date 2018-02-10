<h1 align="center">Blur Shaders</h1>

<h3 align="center">Written by @XorDev</h3>
<h3 align="center">http://xorshaders.weebly.com/tutorials/blur-shaders-5-part-1</h3>
<h3 align="center">A two-part tutorial</h3>

Shaders only process one pixel at a time. For bluring, we want the pixel to mix with the surrounding pixels. That is how we would achieve our blur. The easiest way to do this is to use "texture2D()" multiple times with different coordinates. We will make the radial blur first, because it's easier to understand. Radial blur is actually a form of motion blur, but in our case, motion blur will be side to side while radial will give an illusion of depth. 
Radial blur
The radial blur effect we are creating will be produced by mixing the texture with different scaled versions of the texture. So the simplest way to do that is to scale the texture coordinates. Doubling the coordinates, makes the texture half the size, while halving it doubles the size. The reason to this is because the texture coordinates are in the 0 to 1 range so halving it would make the range 0 to 0.5 so, the texture is stretched to twice the size. So keep this in mind when scaling multiplying the texture coordinates. We will make our first radial blur have 10 sizes and blur between them.
We will multiply the textures coordinates by numbers less than 1 to make the textures slightly smaller. Then we'll average the values returned at those coordinates. Your code should look like this:

    vec4 Color = (texture2D( gm_BaseTexture, v_vTexcoord )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.99+0.5*0.01 )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.98+0.5*0.02 )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.97+0.5*0.03 )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.96+0.5*0.04 )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.95+0.5*0.05 )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.94+0.5*0.06 )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.93+0.5*0.07 )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.92+0.5*0.08 )
    + texture2D( gm_BaseTexture, v_vTexcoord*0.91+0.5*0.09 )) * 0.1;
    gl_FragColor =  Color *  v_vColour;

This code may look confusing, but what it does is rather simple. It gets the average of the different coordinates and centers them with the "+0.5" part. The shader looks like this:

On the left, is the background before the shader, while on the one on the right is after. We will need some knowledge of integers and for statements before we can finish.

&nbsp;

<h3 align="center">Integers</h3>

Integers have the fraction part. It is much like a float, but should always be without the fraction.
So if we set a float to 0 it would cause an error, but if we set and integer to 0, it would work as long as you don't mix it with floats. The only way to mix the different variable types is to use their functions (int() converts floats to ints and float() does the opposite). Here is an example:

    int i = 4;
    float j = 0.5+float(i);

That  code would set i to 4 and j to 4.5. These are used in "for statements" which are particularly useful in blur shaders.

<h3 align="center">Constants</h3>

Constants are variables that are set once and can't be changed. They are constant and so you want to set them first. They are set outside of "void main()" in the format "const variabletype variable = value". An example could be 

    const int Number = 48;

That sets the integer "Number" to 48 as an unchanging constant.

&nbsp;

<h3 align="center">For statement in shaders</h3>

So to start out this shader you'll need to know how to use a "for statement". The for statement repeats a certain code until specified to stop (they cannot repeat more than 255 times).
So platforms do not run loops well, so keep that in mind when making games. When you use them, you must start with defining a variable (most often an integer), then you check if the variable is less then the number of times we want it to repeat (for our example) , and lastly we add to our variable. Rather than using commas to separate the parts, we use semi-colons.
An example would look like this:

    for(int i = 0;i < 16;i++)
    {
    //code that you want to repeat
    }

Now what that does is it sets variable i to 0 (an integer), then checks if i is less than 16. If it is, it will add one to i (i++) and will repeat until i is equal to or greater than 16.

&nbsp;

<h3 align="center">Finishing the radial blur</h3>

Now we will use a for statement to blur more than 10 coordinates. We'll start by adding a constant called "Quality" (make sure it's before the "void main()") and set it to 16, then add this code:

    vec4 Color;
    float v;
    for( float i=0.0;i<1.0;i+=1.0/float(Quality) )
    {
            v = 0.9+i*0.1;//convert "i" to the 0.9 to 1 range
            Color += texture2D( gm_BaseTexture, v_vTexcoord*v+0.5-0.5*v);
    }
    Color /= float(Quality);
    gl_FragColor =  Color *  v_vColour;

This code defines the vector "Color" and the float "v" (they are set to 0). Then the for statement repeats the code the number of times defined by "Quality". The final result of color is averaged by dividing the total by "Quality". Your full code should look something like this:

    varying vec2 v_vTexcoord;
    varying vec4 v_vColour;
    const int Quality = 16;
    
    void main()
    {
        vec4 Color;
        float v;
        for( float i=0.0;i<1.0;i+=1.0/float(Quality) )
        {
            v = 0.9+i*0.1;//convert "i" to the 0.9 to 1 range
            Color += texture2D( gm_BaseTexture, v_vTexcoord*v+0.5-0.5*v);
        }
        Color /= float(Quality);
        gl_FragColor =  Color *  v_vColour;
    }

Quality can be changed to any value that suits your purpose. For smaller textures, use smaller numbers and for larger textures, use larger numbers.
