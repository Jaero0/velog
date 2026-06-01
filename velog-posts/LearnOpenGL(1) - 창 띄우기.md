<p>먼저 GLFW, glad, CMake라는 도구/라이브러리가 필요하다</p>
<p><a href="https://learnopengl.com/Getting-started/Creating-a-window">https://learnopengl.com/Getting-started/Creating-a-window</a></p>
<p>위 링크를 잘 따라서 설치/빌드 추가해주자!</p>
<blockquote>
<p><code>glad.c</code> 설치할때, 무조건 파일 add로 넣어주기!</p>
</blockquote>
<h1 id="glfw로-윈도우-띄우기">glfw로 윈도우 띄우기</h1>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;

int main()
{
    constexpr int width = 1280;
    constexpr int height = 720;

    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    //glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE); //only mac

    return 0;
}</code></pre>
<p>먼저 이런 코드를 작성해주자</p>
<p>openGL을 이용해서 콘솔창에 해당되는 윈도우를 띄우려고 하는 준비과정임</p>
<ol>
<li><code>glfwInit</code>은 glfw를 생성하는 메서드</li>
<li><code>glfwWindowHint</code>는 glfw를 설정하는 메서드임<ul>
<li>첫번째 파라미터는 설정하고자하는 옵션(enum)</li>
<li>두번째 파라미터는 첫번째 파라미터 옵션의 값을 설정함</li>
</ul>
</li>
</ol>
<p><a href="https://www.glfw.org/docs/latest/window.html#window_hints">https://www.glfw.org/docs/latest/window.html#window_hints</a> 
여기에서 사용가능한 enum값들 확인가능</p>
<blockquote>
</blockquote>
<pre><code class="language-cpp">glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);</code></pre>
<p>은 OpenGL 3-major, 3-mionor -&gt; 3.3버전을 사용하고
3.3버전에 맞는 CORE_PROFILE만 사용하겠다는 뜻임</p>
<blockquote>
</blockquote>
<p>OpenGL의 프로필은 3가지가 존재함
<code>Core</code>: 최신 opengl문법만 따름, <code>Deprecated</code>된 문법 사용 못함
<code>Compat</code> : 최신, 과거 모든 opengl문법 호환 가능
<code>any</code>: 낮은 버전(3.2이하)의 opengl을 사용하면 사용해야할 프로필(must be used)</p>
<h2 id="윈도우-객체-만들기">윈도우 객체 만들기</h2>
<p>그 다음은 윈도우 객체를 만들어줘야함
윈도우는 여기서 창임ㅇㅇ</p>
<pre><code class="language-cpp">GLFWwindow* window = glfwCreateWindow(width, height, &quot;LearnOpenGL&quot;, NULL, NULL);
if (window == NULL)
{
    std::cout &lt;&lt; &quot;Failed to create GLFW window&quot; &lt;&lt; std::endl;
    glfwTerminate();
    return -1;
}
glfwMakeContextCurrent(window);</code></pre>
<p>이 코드를 작성</p>
<p><code>glfwCreateWindow</code>의 각 인자</p>
<ol>
<li>첫번째, 두번째 : 띄울 창의 크기</li>
<li>세번째 : 띄울 창의 제목</li>
<li>네번째 : 띄울 창의 전체화면, 창화면 설정<ul>
<li><code>nullptr</code>은 창모드</li>
<li><code>glfwGetPrimaryMonitor()</code>과 같은 함수를 넘기면 해당 모니터에 전체화면!</li>
</ul>
</li>
<li>다섯번째 : 띄울 창의 자원(컨텍스트, 버퍼 등) 공유<ul>
<li><code>nullptr</code>은 자원 공유 안함</li>
<li>다른 창 지정시 해당 창과 opengl로 새로 띄울 창은 자원 공유</li>
</ul>
</li>
</ol>
<p>성공적으로 생성되었으면 <code>glfwMakeContextCurrent</code>를 이용해서 
해당 window객체를 컨텍스트로 지정,
현재 스레드에서 opengl의 코드가 실행되면 해당 window에서 작동하게됨</p>
<h2 id="glad로-gl함수-포인터-체킹">GLAD로 gl함수 포인터 체킹</h2>
<pre><code class="language-cpp">if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
{
    std::cout &lt;&lt; &quot;Failed to initialize GLAD&quot; &lt;&lt; std::endl;
    return -1;
}</code></pre>
<p>위 코드를 이후에 이어붙이자</p>
<ul>
<li>OpenGL은 OS마다 함수 구현 위치가 다름</li>
<li>GLAD는 모든 OpenGL의 함수에 1대1 대응하는 포인터 변수를 가지고 있음</li>
<li><code>glfwGetProcAddress</code>를 통해 OS의 그래픽API를 호출해 해당 함수가 어디에 있는지 알아냄</li>
<li><code>GLADloadproc</code>은 OpenGL의 함수 주소를 GLAD에 해당되는 함수 타입으로 가져오기 위해 필요한 캐스팅 역할</li>
<li><code>gladLoadGLLoader</code>는 GLAD로 캐스팅된 함수를 체킹해서 프로그램 뻗게하든지 계속하든지 ㅇㅇ</li>
</ul>
<h2 id="뷰포트-콜백">뷰포트 콜백</h2>
<pre><code class="language-cpp">void framebuffer_size_callback(GLFWwindow* window, int width, int height); </code></pre>
<p>이런 함수 전방선언을 해주자
아니면 그냥 선언부를 main보다 일찍 배치하든지 ㅇㅇ</p>
<p><code>framebuffer_size_callback</code>은 그냥 우리가 만든 콜백용 함수임
GLFW* window, 윈도우 width, 윈도우 height를 인자로 받아서</p>
<pre><code class="language-cpp">void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}</code></pre>
<p>이런 <code>glViewport</code>에 값을 넣어주는 역할을 함</p>
<p>이때 <code>glViewport</code>함순가 OpenGL의 함수라서 glad가 제대로 프로젝트에 추가되어있지 않으면 에러가 생김</p>
<blockquote>
</blockquote>
<p>glViewport는 (-1,-1)~(1,1) 사이의 값을 인자값으로 받는 4개의 인자의 순서대로 minX,minY maxX,maxY로 매핑을 함</p>
<blockquote>
</blockquote>
<p>위의 예시를 들어 (0,0,800,600)을 했다고 가정해봄
특정 점의 위치가 -0.5,0.5라면, 전체 윈도우 크기를 기준으로 1/4,3/4에 위치를 하고 있는거임.
x: -1일때 0, 1일때 800이니, -0.5면 800의 1/4지점인 200,
y: -1일때 0, 1일때 600이니, 0.5면 600의 3/4지점은 450</p>
<blockquote>
</blockquote>
<p>따라서 설정한 윈도우 기준 (200, 450)의 위치에 점이 찍힘</p>
<p>그 담엔 </p>
<pre><code class="language-cpp">glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);</code></pre>
<p>를 작성해주자</p>
<h2 id="창-띄워보기">창 띄워보기</h2>
<p>이제 실행해보면 창이 나타났다가 바로 꺼지거나, 
에러가 나거나,
창 꺼지는 속도가 너무 빨라서 아무것도 확인 불가능할 수 있음</p>
<pre><code class="language-cpp">while(!glfwWindowShouldClose(window))
{
    glfwSwapBuffers(window);
    glfwPollEvents();    
}</code></pre>
<p>while문으로 창이 종료될때까지 확인하다 종료되면 프로그램을 끝내는 코드임</p>
<ul>
<li><code>glfwPollEvents</code>는 전체적인 이벤트 처리로, 키입력, 마우스입력등에 따라 처리하는 메서드</li>
<li><code>glfwSwapBuffers</code>는 창에 새로운게 그려질때, 창을 갱신하는 메서드임</li>
</ul>
<blockquote>
<p>화면에는 보통 2개의 buffer가 존재함 
하나는 back, 하나는 front</p>
</blockquote>
<p>만약 창에 갱신이 필요할때 buffer를 1개만 이용하면 그 1개로 모든 창을 갱신해야하기때문에 flickering(깜빡임)이슈가 발생됨 </p>
<blockquote>
</blockquote>
<p>이를 해결하기위해 2개의 버퍼를 이용해 갱신을 함
front에는 현재 창에 표시되는 화면이 그려져있음
back에는 새로 그려질 창이 그려지고,
back과 front를 바꾸는게 <code>glfwSwapBuffers</code>메서드임</p>
<p>이제 창이 꺼지면 glfw도 종료를 해야하니</p>
<pre><code class="language-cpp">glfwTerminate();
return 0;</code></pre>
<p>으로 main문을 종료시켜주자</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6f88820f-be1a-4e55-8c49-91f1a118415f/image.png" />
창 잘뜸!</p>
<hr />
<h1 id="키입력">키입력</h1>
<pre><code class="language-cpp">void processInput(GLFWwindow *window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}</code></pre>
<p>상황에 따라 키입력에 대한 인풋도 컨트롤하고 싶어질 수 있으니
위와 같은 processInput이라는 메서드를 만들고 전방선언 ㄱㄱ</p>
<p>esc키가 눌리면 자동으로 glfw로 생성된 창이 종료되고 while문을 빠져나가도록 할거임</p>
<p>따라서 while문은</p>
<pre><code class="language-cpp">while (!glfwWindowShouldClose(window))
{
    processInput(window);

    glfwSwapBuffers(window);
    glfwPollEvents();
} </code></pre>
<p>처럼 수정됨</p>
<hr />
<p>이제 실행해보면 빈 콘솔창만 둥둥 띄워져있을거임</p>
<p>window는 아래처럼 동작을 할거임</p>
<pre><code class="language-cpp">while (!glfwWindowShouldClose(window))
{
    //입력
    processInput(window);

    //렌더링

    //이벤트 확인 및 버퍼 교체
    glfwSwapBuffers(window);
    glfwPollEvents();
} </code></pre>
<p>이게 제대로 작동중인건지 확인해야함</p>
<p>버퍼의 색이 지워지지않고 그대로 계속해서 더해지기만 한다면
결국 검은색이 될거임</p>
<p>그래서 아래와 같은 코드를 <code>while</code>문의 렌더링 부분에 넣어볼거임</p>
<pre><code class="language-cpp">while(!glfwWindowShouldClose(window))
{
    process_input(window);

    //렌더링
    glClearColor(1.0f, 0.6f, 0.6f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);

    glfwSwapBuffers(window);
    glfwPollEvents();    
}</code></pre>
<p>여기서 <code>glClearColor</code>로 버퍼 클리어 할때 무슨색상으로 초기화할지 정하고
<code>glClear</code>를 통해 어떤것을 클리어할지 정함</p>
<ul>
<li><code>GL_COLOR_BUFFER_BIT</code> : 색상 정보</li>
<li><code>GL_COLOR_DEPTH_BIT</code> : 물체 앞뒤 정보</li>
<li><code>GL_COLOR_STENCIL_BIT</code> : 스텐실 정보</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/7878df09-e635-418f-a5ba-d4732d8d2bb1/image.png" /></p>
<h1 id="전체-코드">전체 코드</h1>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void process_input(GLFWwindow *window);

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

    //run window
    while(!glfwWindowShouldClose(window))
    {
        process_input(window);

        glClearColor(1.0f, 0.6f, 0.6f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glfwSwapBuffers(window);
        glfwPollEvents();    
    }

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