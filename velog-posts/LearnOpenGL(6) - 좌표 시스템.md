<h1 id="좌표들">좌표들</h1>
<p>지금까지 우리는 평면만 살펴봤음</p>
<p>이제 이걸 3D로 돌려볼거임
이때 필요한게 전 포스트인 glm에서 사용해본 변환행렬임</p>
<h2 id="공간">공간</h2>
<p>정점과 텍스쳐로 설정된 모든 삼각형 혹은 물체는 공간 변환을 통해 스크린에 출력되게 됨</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/657e19a4-1cfc-42c9-9692-7e188c4a8ac8/image.png" /></p>
<p>이렇게 5개의 공간이 있음</p>
<h3 id="로컬-공간">로컬 공간</h3>
<ul>
<li>물체 자체적으로 사용하는 공간임</li>
<li>가장 기본이 되는, 표현하고자 하는 오브젝트의 좌표(정점)를 표시함</li>
</ul>
<h3 id="월드-공간">월드 공간</h3>
<ul>
<li>태초의 카메라는 정수리에서 발바닥을 찍고 있는 형태임</li>
<li>카메라는 항상 원점이고, 오브젝트들의 좌표를 변경하는거임</li>
<li>오브젝트의 좌표를 변경하는 행렬을 <strong>모델 행렬</strong>이라 부름<ul>
<li>rotate, translate, scale모두 여기에 해당될 수 있음</li>
</ul>
</li>
<li>모델 행렬을 통해 모든 오브젝트들의 좌표는 월드 공간으로 바뀜<h3 id="뷰카메라-공간다음장에서">뷰(카메라) 공간(다음장에서!)</h3>
</li>
<li><strong>뷰 행렬</strong>을 통해 뷰 공간으로 바꿈</li>
<li>뷰 공간은 카메라 시점이라고도 불림</li>
<li>월드 공간의 물체들을 카메라 공간으로 변환하는거임</li>
<li>자세한건 다음 포스팅에서!!<h3 id="클립-공간">클립 공간</h3>
</li>
<li>클리핑 알고리즘의 그 클립임</li>
<li><strong>투영 행렬</strong>을 통해 물체를 짜르고 입체감을 주고 하는거지</li>
<li>뷰 공간 바깥에 존재하는 오브젝트는 잘라버림<ul>
<li>만약 오브젝트의 일부만 뷰 공간에 존재한다면, GL이 자동적으로 삼각형을 재정렬함</li>
</ul>
</li>
</ul>
<p>모든 좌표를 -1~1사이의 좌표로 정의해야 한다면 좀 불편하겠지?
따라서 원하는 좌표 체계를 정의하고
이거를 NDC기억남?
Normalized Device Coordinates라고 GL에서 정의한 정규화된 좌표체계로 변환함</p>
<p>여기서 중요한 개념이 등장하는데</p>
<p>정투영, 원근투영임</p>
<h4 id="원근-분할">원근 분할</h4>
<p>먼저 원근 분할이라는 개념을 알아야함</p>
<p>먼저 행렬이 벡터를 조작하는 함수라는걸 기억할거임
그리고 동차좌표계를 사용해서 n차원일때 n+1개의 원소를 가지는 벡터를 사용한다고도 햇고, 
그렇게 만든 행렬을 이용해서 벡터를 조작하는걸 애파인 변환이라고도 했음!</p>
<p>원근분할은 간단함</p>
<p>$x,y,z,w$가 있을때
$w$값이 원근감을 주기위해 사용되는거임</p>
<p>정투영일때는 각 좌표
$x/w$, $y/w$, $z/w$가 $x,y,z$와 똑같음
$w$가 <code>1.0</code>으로 고정되어야 정투영이거든 ㅇㅇ</p>
<p>원근투영일때는 각 좌표
$x/w$, $y/w$, $z/w$가 $x,y,z$와 다름
$w$가 카메라부터의 거리에 따라 값이 커지게 됨
따라서 좌표 $x,y,z$가 -1~1사이에 있도록 도움을 줌
자 근데 $w$가 커질수록 원근투영에서는 0에 가까워지겠지?
그럼 원점에 모이게 되고, 엄청 작아지게되어 결국 안보이게 되는거임</p>
<h4 id="정투영orthographic">정투영(Orthographic)</h4>
<p>정투영은 말 그대로 원근감 없이 모든 정점간의 거리에 대하여 같은 비율을 적용하는거임</p>
<p>Frustum이라고 불리는데 절두체라는게 있음</p>
<p>원뿔, 삼각뿔 같은거를 중간을 밑면과 평행하게 짜르면 총 2개의 면이 생김</p>
<p>이때 카메라와 가까운쪽은 near, 먼쪽은 far이라고 부름<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/04d7f7a6-38be-4b3d-8214-3a030af1f06e/image.png" />
그리고, 이 near, far frustum사이에 잇는 오브젝트 면을 클립함</p>
<p>바깥에 있는건 무시되는거임 ㅇㅇ</p>
<p>이때 정투영은 사진에서 보이다 싶이
원근이 필요없으므로, near와 far frustum이 1:1비율을 가지게 됨
따라서 아무리 물체가 멀리있어도 원근감이 생기지 않음
그래서 2D에 적합함</p>
<pre><code class="language-cpp">glm::ortho(0.0f, 800.0f, 0.0f, 600.0f, 0.1f, 100.0f);</code></pre>
<p>이런식으로 사용함</p>
<ul>
<li>첫번째, 두번째 : 시작과 끝 너비 좌표</li>
<li>세번째, 네번째 : 시작과 끝 높이 좌표</li>
<li>다섯번째, 여섯번째 : near와 far frustum의 거리</li>
</ul>
<blockquote>
<p>cpu 래스터라이저를 만들어보면
각 레이트레이싱이라는 개념을 접하게 됨
레이트레이싱을 할때 각 픽셀에서 좌표계에 맞춰
<code>0,0,(1 | -1)</code>로 직선을 쏴서 hit되는 부분의 색상을 결정하는걸 구현하게 됨</p>
</blockquote>
<p>이게 정투영임</p>
<h4 id="원근투영perspective">원근투영(perspective)</h4>
<p>정투영과는 다르게 
near와 far의 frustum의 크기가 다름
진짜로 사각뿔을 이용해서 밑면과 평행한 일정부분을 잘라 
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/d8995358-529a-49a2-8c09-0c3ada6ae54b/image.png" />
이런식으로 각 frustum의 비율 차이가 생기게 됨
이 비율이 바로 해상도 비율과 똑같음
16:9뭐 이런거 ㅇㅇ</p>
<pre><code class="language-cpp">glm::mat4 proj = glm::perspective(glm::radians(45.0f), (float)width/(float)height, 0.1f, 100.0f);</code></pre>
<p>이렇게 사용함</p>
<ul>
<li>첫번째 : fov<code>y</code>의 각임
너비의 시야각이 아닌, 높이의 시야각임
위 사진 참고</li>
<li>두번째 : 화면의 종횡비임
16:9, 800:600 이런식ㅇㅇ</li>
<li>세번째, 네번째 : near와 far frustum의 길이</li>
</ul>
<p>$w$값의 계산방식이 궁금하다면
<a href="https://www.songho.ca/opengl/gl_projectionmatrix.html#fov">OpenGL Projection Matric - Projection Matric With FOV</a> 참고 ㄱㄱ</p>
<blockquote>
<p>cpu 래스터라이저를 만들어보면
각 레이트레이싱이라는 개념을 접하게 됨
레이트레이싱을 할때 카메라 좌표(<code>0,0,0</code>)에서 화면의 각 픽셀(<code>x,y,z</code>)로 레이를 쏴서
hit되는 지점의 색상을 결정짓는 방식을 구현하게 됨</p>
</blockquote>
<p>이게 정투영임</p>
<p>정투영과 원근투영의 차이점을 보여주자면
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/2efc60b3-3f61-43d3-aa50-3a52b17da68b/image.png" />
이렇게 됨</p>
<hr />
<p>이제 어떻게 클립 공간을 만드느냐인데...
사실 이건 쉐이더로 넘기면 됨</p>
<p>vertex shader에서 <code>gl_position</code>을 사용해 각 정점을 매핑해준걸 기억해야함!</p>
<p><code>(location = 0) vec4 aPos</code>뭐 이런식으로 값을 받아서 VAO를 이용해 
vertex attribute linking을 해서 정점좌표 매핑해준게 기억나지?</p>
<p>거기서 이것도 해결하면 됨</p>
<p>$V_{clip}=M_{projection}⋅M_{view}⋅M_{model}⋅V_{local}$을 하면됨</p>
<h3 id="스크린-공간">스크린 공간</h3>
<p>클립 공간에서 이제 우리가 처<del>~</del>음에
<code>glViewport</code>설정 해준 그 창의 각 좌표 픽셀로
색상값을 계산해서 gl이 알아서 NDC좌표로 변환해서 화면에 출력하게 됨</p>
<p>그러니까 걱정말고!</p>
<h2 id="사용법">사용법</h2>
<p>간단함!!</p>
<pre><code class="language-cpp">#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 2) in vec2 aTexCoord;


out vec2 texCoord;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{
    gl_Position = projection * view * model * vec4(aPos, 1.0f);
    texCoord = vec2(aTexCoord.x, aTexCoord.y);
} </code></pre>
<p>먼저 이렇게 vertex shader를 수정해줌
3개의 변환 행렬을 받아서
차례대로 aPos -&gt; model -&gt; view -&gt; projection순으로 변환이 이뤄지게 되지비</p>
<p>추가로 여러 상자를 출력하기 위해</p>
<pre><code class="language-cpp">float vertices[] = 
{
    -0.5f, -0.5f, -0.5f,  0.0f, 0.0f,
     0.5f, -0.5f, -0.5f,  1.0f, 0.0f,
     0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
     0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
    -0.5f,  0.5f, -0.5f,  0.0f, 1.0f,
    -0.5f, -0.5f, -0.5f,  0.0f, 0.0f,

    -0.5f, -0.5f,  0.5f,  0.0f, 0.0f,
     0.5f, -0.5f,  0.5f,  1.0f, 0.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 1.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 1.0f,
    -0.5f,  0.5f,  0.5f,  0.0f, 1.0f,
    -0.5f, -0.5f,  0.5f,  0.0f, 0.0f,

    -0.5f,  0.5f,  0.5f,  1.0f, 0.0f,
    -0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
    -0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
    -0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
    -0.5f, -0.5f,  0.5f,  0.0f, 0.0f,
    -0.5f,  0.5f,  0.5f,  1.0f, 0.0f,

     0.5f,  0.5f,  0.5f,  1.0f, 0.0f,
     0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
     0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
     0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
     0.5f, -0.5f,  0.5f,  0.0f, 0.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 0.0f,

    -0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
     0.5f, -0.5f, -0.5f,  1.0f, 1.0f,
     0.5f, -0.5f,  0.5f,  1.0f, 0.0f,
     0.5f, -0.5f,  0.5f,  1.0f, 0.0f,
    -0.5f, -0.5f,  0.5f,  0.0f, 0.0f,
    -0.5f, -0.5f, -0.5f,  0.0f, 1.0f,

    -0.5f,  0.5f, -0.5f,  0.0f, 1.0f,
     0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 0.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 0.0f,
    -0.5f,  0.5f,  0.5f,  0.0f, 0.0f,
    -0.5f,  0.5f, -0.5f,  0.0f, 1.0f
};

glm::vec3 cubePositions[] = 
{
    glm::vec3( 0.0f,  0.0f,  0.0f),
    glm::vec3( 2.0f,  5.0f, -15.0f),
    glm::vec3(-1.5f, -2.2f, -2.5f),
    glm::vec3(-3.8f, -2.0f, -12.3f),
    glm::vec3( 2.4f, -0.4f, -3.5f),
    glm::vec3(-1.7f,  3.0f, -7.5f),
    glm::vec3( 1.3f, -2.0f, -2.5f),
    glm::vec3( 1.5f,  2.0f, -2.5f),
    glm::vec3( 1.5f,  0.2f, -1.5f),
    glm::vec3(-1.3f,  1.0f, -1.5f)
};</code></pre>
<p>이렇게 vertices와 cubePosition이라는걸 이용해서 상자의 각 정점과 포지션을 옮기도록 할거임!</p>
<p>보통 오브젝트는 계속 값이 동적으로 바뀌므로
while문 내부에서 값을 선언해주고 쉐이더로 값을 넘김</p>
<pre><code class="language-cpp">// 모델, 뷰, 투영 행렬
shaderProgram.useShader();
glm::mat4 view = glm::mat4(1.0f);
view = glm::translate(view, glm::vec3(0, 0, -5));
glm::mat4 projection = glm::mat4(1.0f);
projection = glm::perspective(glm::radians(45.0f), (float)windowWidth/windowHeight, 0.1f, 100.0f);

uint32_t viewLoc = glGetUniformLocation(shaderProgram.shaderProgramID, &quot;view&quot;);
glUniformMatrix4fv(viewLoc, 1, GL_FALSE, glm::value_ptr(view));
uint32_t projectionLoc = glGetUniformLocation(shaderProgram.shaderProgramID, &quot;projection&quot;);
glUniformMatrix4fv(projectionLoc, 1, GL_FALSE, glm::value_ptr(projection));

glBindVertexArray(VAO);
for(unsigned int i = 0; i &lt; 10; i++)
{
    // calculate the model matrix for each object and pass it to shader before drawing
    glm::mat4 model = glm::mat4(1.0f);
    model = glm::translate(model, cubePositions[i]);
    float angle = glfwGetTime() * 25.0f; 
    model = glm::rotate(model, glm::radians(angle * 10), glm::vec3(1.0f, 0.3f, 0.5f));
    uint32_t modelLoc = glGetUniformLocation(shaderProgram.shaderProgramID, &quot;model&quot;);
    glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));

    glDrawArrays(GL_TRIANGLES, 0, 36);           
}</code></pre>
<p>이렇게 회전하면서 
cubePosition만큼 움직인 여러 cube들을 만들도록 했음</p>
<blockquote>
</blockquote>
<p>rotate, translate는 model행렬에서 이뤄지는거 유의!</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/76059af4-6d25-4965-98a9-7ec33565053f/image.gif" /></p>
<p>3가지를 확인가능함</p>
<ol>
<li>모든 vertices는 같은 크기임에도 불구하고 원근투영으로 인해 크기가 달라짐</li>
<li>position은 -1~1사이의 NDC가 아님에도 불구하고 자동적으로 정규화가 된 모습</li>
<li>상자의 앞뒤 구분이 없음</li>
</ol>
<p>여기서 앞뒤 구분을 넣어주자</p>
<h2 id="z-buffer">z-buffer</h2>
<p>이거 구현했던거 기억남?</p>
<p><a href="https://velog.io/@vfx_master/%EB%82%98%EB%A7%8C%EC%9D%98-tiny-renderer-%EB%A7%8C%EB%93%A4%EA%B8%B0-4-Z-Buffer-%EC%95%A0%ED%8C%8C%EC%9D%B8%EC%95%84%ED%95%80-%EB%B3%80%ED%99%98">나만의 tiny renderer 만들기 (4) - Z-Buffer, 애파인(아핀) 변환</a></p>
<p>이거를 그냥 OpenGl에서는 한줄로 사용가능함</p>
<p>while이 시작되기전에 </p>
<pre><code class="language-cpp">glEnable(GL_DEPTH_TEST);</code></pre>
<p>이렇게 <code>glEnable</code>, <code>glDisable</code>메서드를 통해 원하는 기능을 켜고 끌 수 있음</p>
<p>여기선 <code>GL_DEPTH_TEST</code>라는 z-buffer 기능을 켜줌으로써 
상자의 앞뒤가 겹치는걸 해결함!</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/27707bc3-7bed-43ee-b197-7cc84c3b79af/image.png" /></p>
<p>근데 막상 넣고 실행하면 이런 검은 화면이 나올거임</p>
<p>이유는 zbuffer를 켜면, 
이전 버퍼의 깊이정보가 그대로 유지되어 
렌더링에 있어 방해가 일어나게됨</p>
<p>따라서</p>
<pre><code class="language-cpp">while(!glfwWindowShouldClose(window))
{
    process_input(window);

    glClearColor(.0f, .0f, .0f, 1.f);
    /*중요!*/glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    //....
}</code></pre>
<p>이렇게 <code>glClear</code>메서드를 통해 Color와 depth 버퍼를 비워주면 됨!!
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/f4fc2f7e-87c3-4f14-988f-e4c06d064875/image.gif" /></p>
<p>끝!!</p>