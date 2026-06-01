<p>화면에 뭔가를 출력하는 행위는 렌더링 파이프라인이라는 것을 통해 픽셀당 색상을 결정하고 출력을 하게 됨</p>
<h1 id="렌더링-파이프라인-간단히">렌더링 파이프라인 간단히</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/1e55885c-c724-4229-9620-f07657d3e286/image.png" /></p>
<p>위 사진의 과정을 거침</p>
<p>사진의 과정을 모두 병렬적으로 이루어짐
그리고 각 단계에서 실행되는 프로그램을 쉐이더라고 부름</p>
<p><strong>vertex</strong>는 오브젝트의 각 정점(꼭짓점) 위치를 나타냄.
큐브형태의 각 면은 각각 2개의 삼각형으로 이루어져있고, 이 삼각형의 정점 위치가 vertex의 좌표가 되는거임</p>
<p>이러한 정점에 대해 필요한 요구사항을 쉐이더로 구현하면 <strong>vertex shader</strong>가 됨
좌표 변형, noise를 이용한 특수효과 등등이 여기에 해당되는 작업임</p>
<blockquote>
<p>필요에 따라 vertex shader에서 geometry shader로 넘어가서 각 정점에 추가적으로 정점을 만들어 원시적인 모양을 추가적으로 만들 수 있음
위의 사진을 보면 정점 하나를 추가해 2개의 삼각형으로 만들어냄ㅇㅇ</p>
</blockquote>
<p>가끔 geometry shader도 구현함
근데 대부분 그냥 안함</p>
<p>shape assembly로 넘어가면 정점을 서로 이어 원시적인 모양을 만들음
정점 3개 -&gt; 삼각형 이런식으로 ㅇㅇ</p>
<p>그리고 이를 rasterization(래스터화, 픽셀화)과정을 거쳐 모니터의 픽셀마다 점을 찍어 표현하게 됨.</p>
<p>이후 <strong>fragment shader</strong>로 넘겨져 각 픽셀에 대하여 연산을 수행하게 됨.
색 입히기, 메탈릭, 픽셀의 alpha값 조절 등이 여기에 해당됨</p>
<p>마지막으로 blending단계로 넘어가, 전체적으로 alpha값 조절, 색상 혼합등의 작업이 일어나게 됨.</p>
<p>보통 vertex, fragment쉐이더를 glsl이나 hlsl이라는 언어로 작성을 하게 됨.</p>
<p>GL에서 다른 파이프라인의 과정은 기본 디폴트값이 존재하는데, vertex, fragment는 구현되어있는게 없음
따라서 이를 직접 구현해줘야함</p>
<h1 id="vertex">vertex</h1>
<p>우리의 모니터, 화면은 모든 물체를 보여주지 않음</p>
<p>잘들어봐 ㅇㅇ</p>
<p>화면의 가장 아래, 왼쪽 부분은 -1,-1임
가장 위, 오른쪽 부분은 1,1임</p>
<p>이게 OpenGL이 NDC(Normalized device coodinates)라는걸 이용해서 화면의 좌표를 정해놓은거임</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/3b609e01-dcc7-4b5a-be60-c3bbdc32c1f2/image.png" /></p>
<p>+$y$축으로 이동은 화면 상단으로 이동하는것을 의미하고,
+$x$축으로 이동은 화면 우측으로 이동하는것을 의미함</p>
<p>이 구간 내에서 화면은 카메라에 들어온 부분을 클리핑 함
즉, 이 좌표를 벗어나는 정점은 표시하지 않는거임</p>
<p>따라서 우리는 좌표를 정의할때, $(-1,-1) ~ (1,1)$(변환후)을 벗어나지 않는 좌표를 정의해줘야함</p>
<p>그리고 우리가 <a href="https://velog.io/@vfx_master/LearnOpenGL1-%EC%B0%BD-%EB%9D%84%EC%9A%B0%EA%B8%B0">LearnOpenGL(1) - 창 띄우기</a>에서 <code>glViewport</code>라는 opengl함수를 이용해서 화면을 정의해줬음</p>
<ol>
<li>여러 좌표를 정의함, 묶음단위? 혹은 필요한대로 정의</li>
<li><code>glViewport</code>에 넘겨준 정보(width, height)를 이용해 정점좌표를 공간의 좌표로 변환함</li>
<li>공간 좌표로 변환된 정점은 fragment로 변환되어 fragment shader의 준비물로 사용됨</li>
</ol>
<pre><code class="language-cpp">float vertices[] = 
{
    -0.5f, -0.5f, 0.0f,
     0.5f, -0.5f, 0.0f,
     0.0f,  0.5f, 0.0f
};  </code></pre>
<p>이런 정점들의 좌표를 선언 ㄱㄱ</p>
<h1 id="vbo---vertex-buffer-object">VBO - vertex buffer object</h1>
<p>vertex은 쉐이더를 통해 색상연산이 되게 됨
vertex 쉐이더는 그래픽 카드에서 가장 첫번째로 수행되는 프로세스임</p>
<p>따라서 vertex를 그래픽카드에서 이용할 수 있도록 해야하는데...</p>
<p>이때 그래픽카드에서 사용될수 있도록 관리하는 오브젝트가
VBO임</p>
<p>기본적으로 GPU내의 연산은 속도가 빠름
rasterize와 병렬 파이프라이닝으로 속도가 어마어마하지ㅇㅇ</p>
<p>근데 우리가 코드로 작성한 모든 정보는 cpu에 들어있음
그리고 CPU에서 gpu로의 통신은 속도가 꽤나 느림...</p>
<p>따라서 가능한한 많은 vertex좌표를 gpu로 보내줘야함</p>
<p>이때 사용하는데 VBO라는거임</p>
<hr />
<h2 id="1-빈-메모리-공간-할당-후-메모리-주소-반환">1. 빈 메모리 공간 할당 후 메모리 주소 반환</h2>
<p><code>glGenBuffers</code>를 이용해서 buffer객체를 생성할 수 있음</p>
<pre><code class="language-cpp">uint32_t VBO;
glGenBuffers(1, &amp;VBO);</code></pre>
<p>먼저 이런 코드를 만들어주자</p>
<ul>
<li>1 : 생성할 버퍼의 갯수</li>
<li>&amp;VBO : 생성된 버퍼 객체를 저장할 배열, 주소, 변수</li>
</ul>
<p>다음은 아래와 같은 코드를 만들어주자
<code>glGenBuffer</code>로 buffer객체를 만들었다면, 
해당 buffer객체에 알맞은 버퍼 유형을 binding해줘야 함</p>
<h2 id="2-gl에-사용중인-메모리-주소를-사용중이라고-알림">2. GL에 사용중인 메모리 주소를 사용중이라고 알림</h2>
<pre><code class="language-cpp">glBindBuffer(GL_ARRAY_BUFFER, VBO);</code></pre>
<p><a href="https://registry.khronos.org/OpenGL-Refpages/gl4/html/glBindBuffer.xhtml">glBindBuffer - OpenGL Reference</a>
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/71b3c1e9-8c2e-4c78-a5ad-8f03daf8163d/image.png" /></p>
<p>이렇게 <code>GL_ARRAT_BUFFER</code>가 vertex 버퍼의 객체 유형이라는 것을 알 수 있음</p>
<h2 id="3-cpu에서-gpu로-정점-버퍼-이동">3. CPU에서 GPU로 정점 버퍼 이동</h2>
<p><code>glBufferData</code>를 사용함</p>
<pre><code class="language-cpp">glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);</code></pre>
<ul>
<li>첫번째 : 어떤 종류의 버퍼에 데이터를 담을지 결정<ul>
<li>위에서 gl에게 vertex buffer를 사용중이라고 말했으므로, 그걸 설정</li>
</ul>
</li>
<li>두번째 : 데이터의 크기</li>
<li>세번째 : 데이터의 주소</li>
<li>네번째 : gpu에서 해당 데이터를 어떻게 사용할지 결정</li>
</ul>
<p>네번째 파라미터가 중요함
몇가지 옵션을 사용할 수 있음</p>
<p>주로사용되는 옵션은 아래 3개임</p>
<ul>
<li><code>GL_STATIC_DRAW</code> : 데이터는 한번만 설정됨, 변경이 자주 일어나지 않음, 한번 설정 후 여러번 사용됨</li>
<li><code>GL_STREAM_DRAW</code> : 데이터는 매 프레임마다 새로 설정됨, 몇번만 사용됨</li>
<li><code>GL_DYNAMIC_DRAW</code> : 데이터는 설정 후 변경이 자주 일어남, 여러번 사용됨</li>
</ul>
<p>이 usage에 따라 gpu의 어느 메모리에 데이터가 저장되고, 사용되고 그런게 정해짐
속도, 접근성 이런게 결정되는거임</p>
<blockquote>
<p>더 많은 옵션이 있지만, gpu의 성능이 좋아지고, 업그레이드 되고, 고도화 되면서
다른 옵션, COPY, READ와 같은 옵션은 거의 필요없어짐
그냥 안건드리는게 더 나은 정도라고 함
<a href="https://registry.khronos.org/OpenGL-Refpages/gl4/html/glBufferData.xhtml">glBufferData - OpenGL Reference</a></p>
</blockquote>
<h1 id="vertex-shader">vertex shader</h1>
<pre><code class="language-cpp">const char *vertexShaderSource = &quot;#version 330 core\n&quot;
&quot;layout (location = 0) in vec3 aPos;\n&quot;
&quot;void main()\n&quot;
&quot;{\n&quot;
&quot;   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n&quot;
&quot;}\0&quot;;</code></pre>
<p>먼저 이런 문자열을 선언하고 시작하자</p>
<p>아직은 쉐이더를 다룰 파트는 아니고,,,
쉐이더를 이용해서 컴파일을 해야하긴 하니 일단 이렇게라도 사용하는거임</p>
<p>대충 설명하면 </p>
<ul>
<li><code>#version 330 core</code> : opengl 3.3버전의 core profile을 명시
(ex, OpenGL 4.2, any profile -&gt; <code>#version 420 any</code>) </li>
<li><code>layout (location = 0)</code> : gpu로 들어온 데이터가 0번이라는 뜻
0번 : 위치(좌표), 1번 : 색상 등등</li>
<li><code>in vec3 aPos</code> : input으로 3차원 aPos가 입력된다는 뜻</li>
<li><code>gl_Position = ...</code> : $n$차원으로 입력된 것은 $n+1$차원으로 변환
동차좌표, 애파인 변환(<a href="https://velog.io/@vfx_master/LearnOpenGL1-%EC%B0%BD-%EB%9D%84%EC%9A%B0%EA%B8%B0">LearnOpenGL(1) - 창 띄우기</a>)</li>
</ul>
<hr />
<p><code>glCreateShader</code>를 통해 shader를 만들어줌</p>
<pre><code class="language-cpp">uint32_t vertexShader = glCreateShader(GL_VERTEX_SHADER);</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/2a9a208c-df1a-40eb-a3cd-58ee8ad79a66/image.png" />
이런 옵션 사용가능, <a href="https://registry.khronos.org/OpenGL-Refpages/gl4/html/glCreateShader.xhtml">glCreateShader - OpenGL Reference</a></p>
<hr />
<pre><code class="language-cpp">glShaderSource(vertexShader, 1, &amp;vertexShaderSource, NULL);</code></pre>
<ul>
<li>첫번째 : 사용하는 쉐이더</li>
<li>두번째 : 쉐이더 소스 내부의 문자열 개수
보통은 glsl, hlsl하나가 큰 문자열 1개이므로 1 전달</li>
<li>세번째 : 쉐이더 소스(<code>const char*</code>)</li>
<li>네번째 : 쉐이더 소스의 길이
<code>\0</code>으로 끝날때까지 소스를 읽음, <code>\0</code>이 아니거나 특정 길이만 읽어야 하면, int타입으로 길이 넣어주셈</li>
</ul>
<hr />
<pre><code class="language-cpp">glCompileShader(vertexShader);</code></pre>
<p>컴파일 해야할 쉐이더를 넣어주기!</p>
<h2 id="특정-쉐이더의-컴파일-성공을-확인하고-싶은-경우">특정 쉐이더의 컴파일 성공을 확인하고 싶은 경우</h2>
<p><code>glGetShaderiv</code>를 사용하면 됨</p>
<pre><code class="language-cpp">int success;
char log[512];
glGetShaderiv(원하는 쉐이더 객체, GL_COMPILE_STATUS, &amp;success);

if (!success)
{
    char log[512];
    glGetShaderInfoLog(원하는 쉐이더 객체, 512, NULL, log);
    std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
}</code></pre>
<p><code>glGetShaderiv</code></p>
<ul>
<li>첫번째 : 상태체크하고 싶은 쉐이더 객체</li>
<li>두번째 : 어떤 상태를 체크하고 싶은지 enum</li>
<li>세번째 : 해당 상태 결과 저장</li>
</ul>
<p><code>glGetShaderInfoLog</code></p>
<ul>
<li>첫번째 : 상태체크에 실패한 쉐이더 객체</li>
<li>두번째 : 에러 메세지 담을 문자 배열 길이</li>
<li>세번째 : 에러 메세지의 길이(NULL -&gt; 전부)</li>
<li>네번째 : 에러 메세지가 저장될 배열</li>
</ul>
<h1 id="fragmentshader">FragmentShader</h1>
<p>vertex shader랑 똑같은 과정을 거치면 됨</p>
<pre><code class="language-cpp">const char *fragmentShaderSource = &quot;#version 330 core\n&quot;
    &quot;out vec4 FragColor;\n&quot;
    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n&quot;
    &quot;}\0&quot;;

 uint32_t fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
 glShaderSource(fragmentShader, 1, &amp;fragmentShaderSource, NULL);
 glCompileShader(fragmentShader);

 glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &amp;success);
 if (!success)
 {
     glGetShaderInfoLog(fragmentShader, 512, NULL, log);
     std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
 }</code></pre>
<h1 id="쉐이더-프로그램">쉐이더 프로그램</h1>
<p>쉐이더를 만들었으면 쉐이더를 하나의 프로그램으로 합쳐줘야함</p>
<p>지금 vertex, fragment쉐이더는 각각 따로 그냥 코드 덩어리에 불과함
이거를 하나의 공정으로 합쳐서 shader라는 공장이 돌아가도록 해야함</p>
<p>그게 쉐이더 프로그램임
여러 쉐이더를 하나의 프로그램으로 묶는걸 Linking이라고 함</p>
<pre><code class="language-cpp">uint32_t shaderProgram = glCreateProgram();
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);</code></pre>
<p>꽤나 직관적인 코드임</p>
<p>createProgram으로 shaderProgram을 만들고,
그 프로그램에 여러 쉐이더를 attach를 한 후
link를 통해 프로그램으로 합침</p>
<p>마찬가지로 프로그램이 제대로 되었는지 확인하는 코드를 만들어줌</p>
<pre><code class="language-cpp">glGetProgramiv(shaderProgram, GL_LINK_STATUS, &amp;success);
if (!success)
{
    glGetProgramInfoLog(shaderProgram, 512, nullptr, log);
    std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
}</code></pre>
<p>중요한건 program은 Link를 하므로,
<code>GL_LINK_STATUS</code>와 <code>glGetProgramiv</code>, <code>glGetProgramInfoLog</code>를 사용해야함</p>
<p>그리고 마지막으로...
gl에게 사용할 프로그램을 사용중이라고 알리면 됨</p>
<p>그리고 링크로 사용된 쉐이더는 delete해주면 됨</p>
<pre><code class="language-cpp">glUseProgram(shaderProgram);
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);</code></pre>
<h1 id="vertex-속성-linking">vertex 속성 Linking</h1>
<p>현재 우린 쉐이더까지 모두 준비가 완료됨</p>
<p>하지만 문제가 있지비...</p>
<p>쉐이더로 보낸 vertex가 대체 어떤 구조로 되어있는지,
어떻게 해석해야할지 gl은 모른다는거임</p>
<p>그래서 그걸 알려줘야됨</p>
<p><code>glVertexAttrib..</code>이라는 엄청나게 다양한 함수와
<code>glVertexAtrribPointer</code>라는 함수가 또 존재함</p>
<p>두개의 차이를 알아보면</p>
<blockquote>
</blockquote>
<table>
<thead>
<tr>
<th>구분</th>
<th><code>glVertexAttrib..</code></th>
<th><code>glVertexAttribPointer</code></th>
</tr>
</thead>
<tbody><tr>
<td>데이터 원본의 위치</td>
<td>CPU(메모리)</td>
<td>GPU(VBO)</td>
</tr>
<tr>
<td>하는일</td>
<td>cpu에서 직접 gpu로 값 전달</td>
<td>vertex 규칙 설정</td>
</tr>
</tbody></table>
<p>이런 차이임</p>
<p>그래서 우리는 gpu로 보내진 VBO의 vertex의 규칙을 설정해줘야하기때문에
사용할것은.... 
<code>glVertexAtrribPointer</code>인거지 ㅇㅇ</p>
<p>근데 또 찾아보면
<code>glVertexAttribPointer</code>, <code>glVertexAttribIPointer</code>, <code>glVertexAttribLPointer</code>
이런 비슷한 함수가 있는데...</p>
<p>이건 vertex의 속성에 따라 결정되는거임</p>
<p>우리는 지금 우리는 shader에서 vec3, vec4와 같은 타입을 이용함
따라서 <code>glVertexAtrribPointer</code>를 사용해야함</p>
<p>만약 shader에서 int, dvec이런걸 사용해야한다면 순서대로
<code>glVertexAttribIPointer</code>, <code>glVertexAttribLPointer</code>를 써야하는거지</p>
<p><code>glVertexAtrribPointer</code>의 원형은 아래 사진과 같음
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/b053dd06-8642-4a50-af31-c337ae8da577/image.png" /></p>
<ul>
<li>첫번째 : 쉐이더에서 설정한 인덱스, 
<code>layout (location = 0)</code>이걸 vertex shader에서 사용하고 있으므로, location 값에 맞춰서 0을 넣어주면 됨</li>
<li>두번째 : 정점 속성의 크기,
<code>layout (location = 0) in vec3 aPos;</code>에서 vec3를 사용하므로 float 3개임
따라서3을 넣어주면 됨</li>
<li>세번째 : VBO안의 데이터가 무슨 타입인지
float 부동소수점이므로, <code>GL_FLOAT</code>을 쓰게됨</li>
<li>네번째 : 정규화할건지 여부
보통 좌표는 정규화 하지 않음</li>
<li>5번째 : 어디까지가 정점 묶음인지, 다음 정점이 시작되기까지 몇 바이트를 건너뛰어야하는지,
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6c9f41d5-6179-418c-9575-5ca514535025/image.png" />
이걸 보면 stride는 3칸을 건너뛰는걸 알수있음, 
따라서 <code>sizeof(float) * 3</code>이 됨</li>
<li>6번째 : 버퍼 내에서 정점 속성이 시작되는 시작점,
제일 처음부터 시작해야한다면 <code>(void*)0</code>이 됨</li>
</ul>
<p>그리고 추가적으로 
<code>glEnableVertexAttribArray</code>를 사용해야하는데,
<code>layout (location = 0)</code>이었으므로 값을 맞춰서
<code>glEnableVertexAttribArray(0)</code>을 해주면 된다</p>
<p>따라서 전체 코드는</p>
<pre><code class="language-cpp">glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 3, (void*)0);
glEnableVertexAttribArray(0);</code></pre>
<p>가 됨!</p>
<h1 id="vao---vertex-array-object">VAO - vertex array object</h1>
<p>VBO는 정점들을 모아놓은, 식재료임
Vertex Attributes는 이 VBO라는 식재료를 어떻게 이용할 수 있는지를 나타내는 레시피임</p>
<p>VAO는 이 두개를 한번에 관리하기 위해 사용하는 일종의 컨테이너임
VBO라는 식재료도 찾고, Vertex Attributes라는 레시피도 찾아서 한번에 해결하려는거임</p>
<p>만약 그려야하는게 1억개인데, 모든 정점에 대해서 직접 Vertex Attributes를 찾아쓰려고 하면 힘들겄지?</p>
<pre><code class="language-cpp">uint32_t VAO;
glGenVertexArrays(1, &amp;VAO);
glBindVertexArray(VAO);</code></pre>
<p>VBO랑 사용법이 거의 비슷함</p>
<p>다만 주의해야할점이 있음</p>
<p>만약 런타임중에 VAO에 값이 계속 추가되면 어떻게 될까?
그 VBO와 Vertex Attributes들이 VAO의 원래 있던 데이터에 영향을 줄 수 있을수도 있음</p>
<p>그래서 명시적으로 모든 바인딩이 끝나면 unbind를 해줌
근데 어짜피 VAO쓰려면 런타임중에 <code>glBindVertexArray</code>를 해줘야해서 의미없기도 함</p>
<h2 id="gldrawarrays란"><code>glDrawArrays</code>란</h2>
<pre><code class="language-cpp">glDrawArrays(GL_TRIANGLES, 0, 3)</code></pre>
<ol>
<li><p>mode
&quot;어떤 형태의 도형을 그릴 것인가?&quot;를 결정함
GL_TRIANGLES: 3개의 정점을 하나의 삼각형으로 묶어서 그림(가장 많이 씀)
GL_POINTS: 정점 하나하나를 점으로 그림
GL_LINES: 정점 2개씩을 묶어서 선으로 그림
GL_TRIANGLE_STRIP: 정점을 이어 붙여서 삼각형을 연속으로 그림</p>
</li>
<li><p>first
VBO의 몇 번째 정점부터 시작할 것인가를 나타내는 인덱스임
보통 처음부터 그리기 시작하므로 0을 넣음</p>
</li>
<li><p>count
총 몇 개의 정점을 그릴 것인가를 정함
예를 들어, 삼각형 1개를 그리려면 정점이 3개 필요하므로 3을 넣음
만약 정점 배열에 6개의 정점이 있는데 count를 3으로 설정하면, 앞의 삼각형 1개만 그려짐</p>
</li>
</ol>
<h1 id="전체-코드">전체 코드</h1>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void process_input(GLFWwindow *window);

constexpr float vertices[] = 
{
    -0.5f, -0.5f, 0.0f,
     0.5f, -0.5f, 0.0f,
     0.0f,  0.5f, 0.0f
};  

const char *vertexShaderSource = &quot;#version 330 core\n&quot;
    &quot;layout (location = 0) in vec3 aPos;\n&quot;
    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n&quot;
    &quot;}\0&quot;;

const char *fragmentShaderSource = &quot;#version 330 core\n&quot;
    &quot;out vec4 FragColor;\n&quot;
    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n&quot;
    &quot;}\0&quot;;

int main()
{
    constexpr int width = 1280;
    constexpr int height = 720;

    //glfw init
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    //make window with glfw
    GLFWwindow* window = glfwCreateWindow(width, height, &quot;HukPak&quot;, nullptr, nullptr);
    if (window == NULL)
    {
        std::cout &lt;&lt; &quot;Failed to create GLFW window&quot; &lt;&lt; std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);

    //GLAD checks gl pointer of GL-functions
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
    {
        std::cout &lt;&lt; &quot;Failed to initialize GLAD&quot; &lt;&lt; std::endl;
        return -1;
    }

    //viewport configuration with custom callback method
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    // 1. Vertex shader
    uint32_t vertexShader = glCreateShader(GL_VERTEX_SHADER);
    glShaderSource(vertexShader, 1, &amp;vertexShaderSource, NULL);
    glCompileShader(vertexShader);

    // 2. check vertex shader successfully compiled
    int success;
    char log[512];
    glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &amp;success);
    if (!success)
    {
        glGetShaderInfoLog(vertexShader, 512, NULL, log);
        std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
    }

    // 3. fragment shader
    uint32_t fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(fragmentShader, 1, &amp;fragmentShaderSource, NULL);
    glCompileShader(fragmentShader);

    // 4. check fragment shader successfully compiled
    glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &amp;success);
    if (!success)
    {
        glGetShaderInfoLog(fragmentShader, 512, NULL, log);
        std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
    }

    // 5. shader program
    uint32_t shaderProgram = glCreateProgram();
    glAttachShader(shaderProgram, vertexShader);
    glAttachShader(shaderProgram, fragmentShader);
    glLinkProgram(shaderProgram);

    // 6. check shader program successfully compiled
    glGetProgramiv(shaderProgram, GL_LINK_STATUS, &amp;success);
    if (!success)
    {
        glGetProgramInfoLog(shaderProgram, 512, nullptr, log);
        std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
    }

    // 7. delete already linked shader
    glDeleteShader(vertexShader);
    glDeleteShader(fragmentShader);

    // 8. VAO
    uint32_t VAO;
    glGenVertexArrays(1, &amp;VAO);
    glBindVertexArray(VAO);

    // 9. Vertex, VBO
    uint32_t VBO;
    glGenBuffers(1, &amp;VBO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    // 9-1. vertex attributes linking
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 3, (void*)0);
    glEnableVertexAttribArray(0);

    // 9-2. tell gl, binding is over. So don't let bind more to VAO
    glBindVertexArray(0);

    //run window
    while(!glfwWindowShouldClose(window))
    {
        process_input(window);

        glClearColor(1.0f, 0.6f, 0.6f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        // 10. use program / Binding VAO / draw with VAO
        glUseProgram(shaderProgram);
        glBindVertexArray(VAO);
        glDrawArrays(GL_TRIANGLES, 0, 3);

        //swapping buffer frame, and execute event as like input or something
        glfwSwapBuffers(window);
        glfwPollEvents();    
    }

    //optional, don't need it but keep do it until used to OpenGL
    glDeleteBuffers(1, &amp;VBO);
    glDeleteProgram(shaderProgram);
    glDeleteVertexArrays(1, &amp;VAO);

    //stop glfw with runtime over
    glfwTerminate();
    return 0;
}

//viewport configuration callback method
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}

//key input callback
void process_input(GLFWwindow *window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}</code></pre>
<h1 id="ebo---element-buffer-object">EBO - Element buffer object</h1>
<pre><code class="language-cpp">float vertices[] = 
{
    // 첫 번째 삼각형
     0.5f,  0.5f, 0.0f,  // 오른쪽 위
     0.5f, -0.5f, 0.0f,  // 오른쪽 아래
    -0.5f,  0.5f, 0.0f,  // 왼쪽 위 
    // 두 번째 삼각형
     0.5f, -0.5f, 0.0f,  // 오른쪽 아래
    -0.5f, -0.5f, 0.0f,  // 왼쪽 아래
    -0.5f,  0.5f, 0.0f   // 왼쪽 위
};</code></pre>
<p>이렇게 두개의 삼각형을 그린다고 가정해보자</p>
<pre><code class="language-cpp">glDrawArrays(GL_TRIANGLES, 0, 6);</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/d77af933-1fec-4900-a8b8-71eff58f8f14/image.png" />
이렇게 6개의 정점을 그리면 이런 사각형이 만들어짐</p>
<p>근데 <code>vertices</code>를 잘 보면 공통 꼭지점이 생김
즉, 사각형은 4개의 꼭지점만 있으면 되는데, 6개의 꼭지점으로 이를 표현하고 있는거임
이럼 안되는거임
리소스 낭비임</p>
<p>이걸 해결해주는 개념이 EBO임</p>
<p>VBO랑 거의 그냥 똑같음
<code>glBindBuffer</code>나 무슨 함수써야할때 
<code>GL_ARRAY_BUFFER</code>가 아닌 <code>GL_ELEMENT_ARRAY_BUFFER</code>를 사용해주면 됨</p>
<p>그리고 draw를 할때 <code>glDrawArray</code>가 아닌 <code>glDrawElements</code>를 해주면 댐!</p>
<p>그럼 뭘해야하냐...</p>
<ol>
<li>정점과 그것들의 인덱스 지정</li>
<li>EBO buffer를 만들어주고 설정</li>
<li><code>glDrawElements</code>로 렌더링</li>
</ol>
<blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/02fa7bfb-700e-49da-9d16-153fb2daafa4/image.png" />
여기서 중요한 파라미터가 있음</p>
</blockquote>
<ul>
<li>첫번째 : 어떤 모드로 그리고 싶은지 설정, <code>glDrawArrays</code>랑 똑같음</li>
<li>두번째 : 몇개의 정점을 그려야 하는지, indices배열의 size랑 똑같음</li>
<li>세번째 : 우리가 사용할 indices배열은 무슨 타입의 배열인지, 
이걸 설정해야 gl이 적절한 바이트만큼씩 끊어서 값을 읽음</li>
<li>네번째 : EBO에서 얼마만큼 인덱스를 건너뛰어 시작할건지(offset)<ul>
<li>만약 type으로 GL_UNSIGNED_INT를 사용 중이라면:
unsigned int는 4바이트임
따라서 0은 첫 번째 인덱스를 의미함
만약 5번째 인덱스부터 그리기 시작하고 싶다면? 
<code>(void*)(4 * 4)</code> 즉, 16바이트 오프셋을 전달해야 함</li>
</ul>
</li>
</ul>
<p>아래 코드임</p>
<pre><code class="language-cpp">float vertices[] = 
{
    0.5f,  0.5f, 0.0f,  // 우상단
    0.5f, -0.5f, 0.0f,  // 우하단
   -0.5f, -0.5f, 0.0f,  // 좌하단
   -0.5f,  0.5f, 0.0f,   // 좌상단

    0.6f, 0.6f, 0.0f, //삼각형추가
    0.5f, 0.6f, 0.0f,
    0.55f, 0.7f, 0.0f 
};

uint32_t indices[] = 
{
    0, 1, 3, //첫번째 삼각형 좌표 인덱스
    1, 2, 3 //두번째 삼각형 좌표 인덱스
};

//...
    uint32_t VBO;
    glGenBuffers(1, &amp;VBO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    uint32_t EBO;
    glGenBuffers(1, &amp;EBO);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 3, (void*)0);
    glEnableVertexAttribArray(0);

    glBindVertexArray(0);

    while(!glfwWindowShouldClose(window))
    {
        //...

        // 10. use program / delete linked shader
        glUseProgram(shaderProgram);
        glBindVertexArray(VAO);
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
        glBindVertexArray(0);

        //...
    }

    //...

    //stop glfw with runtime over
    glfwTerminate();
    return 0;

//...</code></pre>
<h1 id="전체-코드-1">전체 코드</h1>
<p>추가로 삼각형 하나 더 그리도록 코드를 약간 수정해봄</p>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void process_input(GLFWwindow *window);

float vertices[] = 
{
    0.5f,  0.5f, 0.0f,  // 우상단
    0.5f, -0.5f, 0.0f,  // 우하단
   -0.5f, -0.5f, 0.0f,  // 좌하단
   -0.5f,  0.5f, 0.0f,   // 좌상단

    0.6f, 0.6f, 0.0f, //삼각형추가
    0.5f, 0.6f, 0.0f,
    0.55f, 0.7f, 0.0f,

    -0.8f, -0.8f, 0.0f, //wireframe용 삼각형
    -0.6f, -0.8f, 0.0f,
    -0.7f, -0.6f, 0.0f
};

uint32_t indices[] = 
{
    0, 1, 3, //첫번째 삼각형 좌표 인덱스
    1, 2, 3, //두번째 삼각형 좌표 인덱스
    4,5,6
};

const char *vertexShaderSource = &quot;#version 330 core\n&quot;
    &quot;layout (location = 0) in vec3 aPos;\n&quot;
    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n&quot;
    &quot;}\0&quot;;

const char *fragmentShaderSource = &quot;#version 330 core\n&quot;
    &quot;out vec4 FragColor;\n&quot;
    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n&quot;
    &quot;}\0&quot;;

int main()
{
    constexpr int width = 1280;
    constexpr int height = 720;

    //glfw init
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    //make window with glfw
    GLFWwindow* window = glfwCreateWindow(width, height, &quot;HukPak&quot;, nullptr, nullptr);
    if (window == NULL)
    {
        std::cout &lt;&lt; &quot;Failed to create GLFW window&quot; &lt;&lt; std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);

    //GLAD checks gl pointer of GL-functions
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
    {
        std::cout &lt;&lt; &quot;Failed to initialize GLAD&quot; &lt;&lt; std::endl;
        return -1;
    }

    //viewport configuration with custom callback method
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    // 1. Vertex shader
    uint32_t vertexShader = glCreateShader(GL_VERTEX_SHADER);
    glShaderSource(vertexShader, 1, &amp;vertexShaderSource, NULL);
    glCompileShader(vertexShader);

    // 2. check vertex shader successfully compiled
    int success;
    char log[512];
    glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &amp;success);
    if (!success)
    {
        glGetShaderInfoLog(vertexShader, 512, NULL, log);
        std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
    }

    // 3. fragment shader
    uint32_t fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(fragmentShader, 1, &amp;fragmentShaderSource, NULL);
    glCompileShader(fragmentShader);

    // 4. check fragment shader successfully compiled
    glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &amp;success);
    if (!success)
    {
        glGetShaderInfoLog(fragmentShader, 512, NULL, log);
        std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
    }

    // 5. shader program
    uint32_t shaderProgram = glCreateProgram();
    glAttachShader(shaderProgram, vertexShader);
    glAttachShader(shaderProgram, fragmentShader);
    glLinkProgram(shaderProgram);

    // 6. check shader program successfully compiled
    glGetProgramiv(shaderProgram, GL_LINK_STATUS, &amp;success);
    if (!success)
    {
        glGetProgramInfoLog(shaderProgram, 512, nullptr, log);
        std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
    }

    // 7. delete already linked shader
    glDeleteShader(vertexShader);
    glDeleteShader(fragmentShader);

    // 8. VAO
    uint32_t VAO;
    glGenVertexArrays(1, &amp;VAO);
    glBindVertexArray(VAO);

    // 9. Vertex, VBO
    uint32_t VBO;
    glGenBuffers(1, &amp;VBO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    // 9-1. EBO
    uint32_t EBO;
    glGenBuffers(1, &amp;EBO);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

    // 9-2. vertex attributes linking
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 3, (void*)0);
    glEnableVertexAttribArray(0);

    // 9-3. tell gl, binding is over. So don't let bind more to VAO
    glBindVertexArray(0);

    //run window
    while(!glfwWindowShouldClose(window))
    {
        process_input(window);

        glClearColor(.0f, .0f, .0f, 1.f);
        glClear(GL_COLOR_BUFFER_BIT);

        // 10. use program / delete linked shader
        glUseProgram(shaderProgram);
        glBindVertexArray(VAO);

        // additional triangle render with DrawArrays + DrawElements
        glDrawElements(GL_TRIANGLES, 9, GL_UNSIGNED_INT, 0);
        //glDrawArrays(GL_TRIANGLES, 4, 3);
        //glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        // draw 1 trangle with wireframe mode
        glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
        glDrawArrays(GL_TRIANGLES, 7, 3);
        glPolygonMode(GL_FRONT_AND_BACK, GL_FILL);

        glBindVertexArray(0);

        //swapping buffer frame, and execute event as like input or something
        glfwSwapBuffers(window);
        glfwPollEvents();    
    }

    //optional, don't need it but keep do it until used to OpenGL
    glDeleteBuffers(1, &amp;VBO);
    glDeleteProgram(shaderProgram);
    glDeleteVertexArrays(1, &amp;VAO);

    //stop glfw with runtime over
    glfwTerminate();
    return 0;
}

//viewport configuration callback method
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}

//key input callback
void process_input(GLFWwindow *window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/b2001183-3cba-41af-9a37-59c3f0094ffc/image.png" /></p>