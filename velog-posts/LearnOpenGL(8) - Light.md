<p><a href="https://learnopengl.com/Lighting/Colors">LearnOpenGL - Colors</a></p>
<p>여기 이걸보고 코드를 수정하면 되는데...
일단 빛반사와 관련된 내용이라 </p>
<p>빛 반사는 </p>
<ol>
<li>태양 백색광에서 빛이 나온다</li>
<li>특정 물체의 표면의 색상에 따라 일부 파장은 흡수되고, 일부 파장은 반사된다<ul>
<li>빨간색 표면이면 빨간색 계열을 만드는데 필요한 파장이 반사됨
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/e8ad7a69-a153-4327-bfd9-d349a5ff21f3/image.png" /></li>
</ul>
</li>
<li>반사된 빛이 우리 눈에 보인다</li>
</ol>
<p>이게 전부임</p>
<p>그냥 코드를 좀 고침</p>
<blockquote>
<p>중요!!!</p>
</blockquote>
<p>내가 코드를 직접 주석달고, 동작원리를 주석으로 설명해놓음
그러니 이해가 안되더라도 한번 보고 넘어가자</p>
<p><code>main.cpp</code></p>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;

#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;
#include &lt;glm/gtc/type_ptr.hpp&gt;

#include &lt;iostream&gt;
#include &quot;Shader.h&quot;
#include &quot;Camera.h&quot;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void mouse_callback(GLFWwindow* window, double xpos, double ypos);
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset);
void processInput(GLFWwindow *window);

// 윈도우 사이즈
const uint32_t SCR_WIDTH = 1280;
const uint32_t SCR_HEIGHT = 720;

// 카메라
Camera camera(glm::vec3(0.0f, 0.0f, 3.0f));
float lastX = SCR_WIDTH / 2.0f;
float lastY = SCR_HEIGHT / 2.0f;
bool firstMouse = true;

// 프레임 보정
float deltaTime = 0.0f;
float lastFrame = 0.0f;

// 라이팅
glm::vec3 lightPos(1.2f, 1.0f, 2.0f);

int main()
{
    // glfw 초기화, 설정
    // ----------------
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    // glfw 창 생성
    // ------------
    GLFWwindow* window = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, &quot;흑45&quot;, nullptr, nullptr);
    if (window == nullptr)
    {
        std::cout &lt;&lt; &quot;Failed to create GLFW window&quot; &lt;&lt; std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);
    // glfw 인풋 콜백 처리
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
    glfwSetCursorPosCallback(window, mouse_callback);
    glfwSetScrollCallback(window, scroll_callback);

    // glfw 사용자 마우스 설정
    glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED);

    // glad로 openGL모든 함수 포인터 로딩
    // -------------------------------
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
    {
        std::cout &lt;&lt; &quot;Failed to initialize GLAD&quot; &lt;&lt; std::endl;
        return -1;
    }

    // openGL 설정
    // -----------
    glEnable(GL_DEPTH_TEST);

    // 쉐이더 프로그램 만들기
    // -------------------
    Shader normalCubeShader(&quot;VertexShaders/ColorShader.vsh&quot;, &quot;FragmentShaders/ColorShader.fsh&quot;);
    Shader lightCubeShader(&quot;VertexShaders/LightShader.vsh&quot;, &quot;FragmentShaders/LightShader.fsh&quot;);

    // 정점 데이터
    // ----------
    float vertices[] = {
        -0.5f, -0.5f, -0.5f, 
         0.5f, -0.5f, -0.5f,  
         0.5f,  0.5f, -0.5f,  
         0.5f,  0.5f, -0.5f,  
        -0.5f,  0.5f, -0.5f, 
        -0.5f, -0.5f, -0.5f, 

        -0.5f, -0.5f,  0.5f, 
         0.5f, -0.5f,  0.5f,  
         0.5f,  0.5f,  0.5f,  
         0.5f,  0.5f,  0.5f,  
        -0.5f,  0.5f,  0.5f, 
        -0.5f, -0.5f,  0.5f, 

        -0.5f,  0.5f,  0.5f, 
        -0.5f,  0.5f, -0.5f, 
        -0.5f, -0.5f, -0.5f, 
        -0.5f, -0.5f, -0.5f, 
        -0.5f, -0.5f,  0.5f, 
        -0.5f,  0.5f,  0.5f, 

         0.5f,  0.5f,  0.5f,  
         0.5f,  0.5f, -0.5f,  
         0.5f, -0.5f, -0.5f,  
         0.5f, -0.5f, -0.5f,  
         0.5f, -0.5f,  0.5f,  
         0.5f,  0.5f,  0.5f,  

        -0.5f, -0.5f, -0.5f, 
         0.5f, -0.5f, -0.5f,  
         0.5f, -0.5f,  0.5f,  
         0.5f, -0.5f,  0.5f,  
        -0.5f, -0.5f,  0.5f, 
        -0.5f, -0.5f, -0.5f, 

        -0.5f,  0.5f, -0.5f, 
         0.5f,  0.5f, -0.5f,  
         0.5f,  0.5f,  0.5f,  
         0.5f,  0.5f,  0.5f,  
        -0.5f,  0.5f,  0.5f, 
        -0.5f,  0.5f, -0.5f, 
    };

    // VBO, cubeVAO를 만들고 생성
    uint32_t VBO, cubeVAO;
    glGenVertexArrays(1, &amp;cubeVAO);
    glGenBuffers(1, &amp;VBO);

    //VBO를 GL_ARRAY_BUFFER에 바인딩(상태등록)
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    //현재 gl GL_ARRAY_BUFFER에 등록된 상태인 VBO버퍼에게 사용할 정점 데이터 전달
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    //VAO를 VertexArray에 바인딩(상태등록)
    glBindVertexArray(cubeVAO);
    //현재 gl vertexArray에 등록된 상태인 cubeVAO에게
    //VBO에 전달된 정점 데이터를 어떤식으로 사용해야하는지 vertexAttribute 처리
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
    //현재 gl vertexArray에 등록된 상태인 cubeVAO를 사용하는 쉐이더의 0번 location으로 데이터 전달 준비
    glEnableVertexAttribArray(0);

    //lightCubeVAO를 만들고 VertexArray생성
    //VAO를 VertexArray에 바인딩(상태등록) -&gt; cubeVAO에서 lightCubeVAO로 상태바뀜
    uint32_t lightCubeVAO;
    glGenVertexArrays(1, &amp;lightCubeVAO);
    glBindVertexArray(lightCubeVAO);

    //이미 GL_ARRAY_BUFFER에 VBO가 바인딩 되어있긴하지만,
    //명시적으로 바인딩 체킹 하는 습관을 가집시다
    glBindBuffer(GL_ARRAY_BUFFER, VBO);

    ////현재 gl vertexArray에 등록된 상태인 lightCubeVAO에게
    //VBO에 전달된 정점 데이터를 어떤식으로 사용해야하는지 vertexAttribute 처리
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
    //현재 gl vertexArray에 등록된 상태인 lightCubeVAO를 사용하는 쉐이더의 0번 location으로 데이터 전달 준비
    glEnableVertexAttribArray(0);


    // 렌더링 루프(매 프레임 실행)
    // -----------
    while (!glfwWindowShouldClose(window))
    {
        // 프레임 보정
        // ----------
        float currentFrame = static_cast&lt;float&gt;(glfwGetTime());
        deltaTime = currentFrame - lastFrame;
        lastFrame = currentFrame;

        // 인풋
        // ----
        processInput(window);

        // 드로우 시작!
        // ----------
        glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

        //cubeVAO에 사용할 쉐이더인 normalCubeShader프로그램을 GL에게 쉐이더 상태 등록
        normalCubeShader.use();
        normalCubeShader.setVec3(&quot;objectColor&quot;, 1.0f, 0.5f, 0.31f);
        normalCubeShader.setVec3(&quot;lightColor&quot;,  1.0f, 1.0f, 1.0f);

        // world-&gt;view로 가는 view변환행렬/view-&gt;screen으로 가는 projection 변환 행렬 만들기
        glm::mat4 projection = glm::perspective(glm::radians(camera.Zoom), (float)SCR_WIDTH / (float)SCR_HEIGHT, 0.1f, 100.0f);
        glm::mat4 view = camera.GetViewMatrix();
        normalCubeShader.setMat4(&quot;projection&quot;, projection);
        normalCubeShader.setMat4(&quot;view&quot;, view);

        // local-&gt;world로 가는 model변환행렬 만들기
        glm::mat4 model = glm::mat4(1.0f);
        normalCubeShader.setMat4(&quot;model&quot;, model);

        //cubeVAO를 GL에게 VertexArray로 바인딩(상태등록)
        //normalCubeShader가 현재 shaderProgram등록되어 있으므로,
        //Draw를 할때, 
        //cubeVAO에 저장한 vertex Attribute를 
        //enableVertexAttribArray의 0번 등록해놨으니
        //normalCubeShader의 location = 0으로 해당 정점 속성 데이터 전달
        glBindVertexArray(cubeVAO);
        glDrawArrays(GL_TRIANGLES, 0, 36);


        //lightCubeVAO에 사용할 쉐이더인 lightCubeShader프로그램을 GL에게 쉐이더 상태 등록
        lightCubeShader.use();
        //변환행렬들 생성
        lightCubeShader.setMat4(&quot;projection&quot;, projection);
        lightCubeShader.setMat4(&quot;view&quot;, view);
        model = glm::mat4(1.0f);
        model = glm::translate(model, lightPos);
        model = glm::scale(model, glm::vec3(0.2f)); // a smaller cube
        lightCubeShader.setMat4(&quot;model&quot;, model);

        //lightCubeVAO를 GL에게 VertexArray로 바인딩(상태등록)
        //lightCubeShader가 현재 shaderProgram등록되어 있으므로,
        //Draw를 할때, 
        //lightCubeVAO에 저장한 vertex Attribute를 
        //enableVertexAttribArray의 0번 등록해놨으니
        //lightCubeShader의 location = 0으로 해당 정점 속성 데이터 전달
        glBindVertexArray(lightCubeVAO);
        glDrawArrays(GL_TRIANGLES, 0, 36);


        // glfw 버퍼 스와핑
        // ---------------
        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    //안해도 되긴하는데, 명시적으로 프로그램 종료될때
    //Buffer, VertexArray를 메모리에서 삭제
    // ---------------------------------------
    glDeleteVertexArrays(1, &amp;cubeVAO);
    glDeleteVertexArrays(1, &amp;lightCubeVAO);
    glDeleteBuffers(1, &amp;VBO);

    //프로그램 종료시 glfw도 끝내기!
    // ----------
    glfwTerminate();
    return 0;
}

// 키 인풋 콜백 이벤트
// -----------------
void processInput(GLFWwindow *window)
{
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);

    if (glfwGetKey(window, GLFW_KEY_W) == GLFW_PRESS)
        camera.ProcessKeyboard(FORWARD, deltaTime);
    if (glfwGetKey(window, GLFW_KEY_S) == GLFW_PRESS)
        camera.ProcessKeyboard(BACKWARD, deltaTime);
    if (glfwGetKey(window, GLFW_KEY_A) == GLFW_PRESS)
        camera.ProcessKeyboard(LEFT, deltaTime);
    if (glfwGetKey(window, GLFW_KEY_D) == GLFW_PRESS)
        camera.ProcessKeyboard(RIGHT, deltaTime);
}

// glfw를 이용해 gl에게 사용할 창의 viewport크기 지정
// ---------------------------------------------
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    // make sure the viewport matches the new window dimensions; note that width and 
    // height will be significantly larger than specified on retina displays.
    glViewport(0, 0, width, height);
}


// 마우스 이동 콜백 이벤트
// --------------------
void mouse_callback(GLFWwindow* window, double xposIn, double yposIn)
{
    float xpos = static_cast&lt;float&gt;(xposIn);
    float ypos = static_cast&lt;float&gt;(yposIn);

    if (firstMouse)
    {
        lastX = xpos;
        lastY = ypos;
        firstMouse = false;
    }

    float xoffset = xpos - lastX;
    float yoffset = lastY - ypos; // reversed since y-coordinates go from bottom to top

    lastX = xpos;
    lastY = ypos;

    camera.ProcessMouseMovement(xoffset, yoffset);
}

// 마우스 스크롤 콜백 이벤트
// ---------------------
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset)
{
    camera.ProcessMouseScroll(static_cast&lt;float&gt;(yoffset));
}</code></pre>
<p><a href="https://learnopengl.com/code_viewer_gh.php?code=includes/learnopengl/shader_m.h">shader.h</a>
<a href="https://learnopengl.com/code_viewer_gh.php?code=src/2.lighting/1.colors/1.colors.vs">ColorShader.vsh</a>
<a href="https://learnopengl.com/code_viewer_gh.php?code=src/2.lighting/1.colors/1.colors.fs">ColorShader.fsh</a>
<a href="https://learnopengl.com/code_viewer_gh.php?code=src/2.lighting/1.colors/1.light_cube.vs">LightShader.vsh</a>
<a href="https://learnopengl.com/code_viewer_gh.php?code=src/2.lighting/1.colors/1.light_cube.fs">LightShader.fsh</a></p>
<p>그 외에는 위 코드 참고해서 수정!</p>
<h1 id="퐁-조명">퐁 조명</h1>
<p>그냥 항상 퐁조명부터 시작함
PBR은 나중에 할거</p>
<p><code>ColorShader.fsv</code>를 수정해줄거임</p>
<p>왜냐면 색상은 fragment에서 결정되거든ㅇㅇ</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/c6c9f5f2-ae18-4e6e-a18c-a18130279f78/image.png" /></p>
<p>차례대로 만들어보자</p>
<h2 id="ambient">ambient</h2>
<p>아무리 어두워도 어디선가 나오는 빛 덕분에
물체는 살짝이라도 빛이남!
그걸 표현하는거임</p>
<blockquote>
<p><strong>Abient</strong> = Ambient 세기 * 입사광 색
<strong>Abient 색</strong> = 물체 색 * Abient</p>
</blockquote>
<p>이거임</p>
<p>그럼 glsl을 수정해보자</p>
<pre><code class="language-cpp">#version 330 core
out vec4 FragColor;

uniform vec3 objectColor; //물체색
uniform vec3 lightColor; //입사광

void main()
{
    float ambientStrength = 0.1f;
    vec3 ambient = ambientStrength * lightColor;

    vec3 ambientColor = objectColor * ambient;
    FragColor = vec4(ambientColor, 1.0);
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/84cc936b-79cc-4d53-ab7d-185f4dc1e19d/image.png" /></p>
<p>EASY~~</p>
<h2 id="diffuse">diffuse</h2>
<p>공식은 간단함</p>
<blockquote>
<p>$Diffuse = |Normal| \cdot |lightDir| * lightColor$</p>
</blockquote>
<h3 id="표면-법선-벡터">표면 법선 벡터</h3>
<p>diffuse는 오브젝트 전체의 기본 색상을 결정하는 거임</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/2d002255-27d5-48dc-9648-806dc7129ae4/image.png" /></p>
<p>이 사진을 설명해봄</p>
<ol>
<li>입사광이 있음</li>
<li>법선(normal)이 있음</li>
<li>입사광 벡터와 법선 벡터의 내적이 ($\leq 90\degree$) 1에 가까울수록 반사되는 빛의 양이 많아지고, 방향이 달라질때는 0에 더 가까워짐<ul>
<li>이때 두 벡터는 모두 normalized된 상태여야함!!</li>
</ul>
</li>
</ol>
<p>이때 법선벡터는 표면(fragment)에 대한 벡터지, 정점에 대한 normal이 아님</p>
<blockquote>
<p>평면 법선벡터 구하는법</p>
</blockquote>
<p>삼각형은 3개의 정점이 있음
이 중 순서에 맞춰 $\overrightarrow{ab}$ 와 $\overrightarrow{ac}$를 구해서
두 벡터의 외적을 하면 법선벡터가 나옴</p>
<p>근데 귀찮으니
어짜피 평면 큐브니까 간단하기도 하고 해서...</p>
<pre><code class="language-cpp">float vertices[] = 
{
    //정점 좌표               //normal벡터
    -0.5f, -0.5f, -0.5f,    0.0f,  0.0f, -1.0f,
     0.5f, -0.5f, -0.5f,    0.0f,  0.0f, -1.0f, 
     0.5f,  0.5f, -0.5f,    0.0f,  0.0f, -1.0f, 
     0.5f,  0.5f, -0.5f,    0.0f,  0.0f, -1.0f, 
    -0.5f,  0.5f, -0.5f,    0.0f,  0.0f, -1.0f, 
    -0.5f, -0.5f, -0.5f,    0.0f,  0.0f, -1.0f, 

    -0.5f, -0.5f,  0.5f,    0.0f,  0.0f, 1.0f,
     0.5f, -0.5f,  0.5f,    0.0f,  0.0f, 1.0f,
     0.5f,  0.5f,  0.5f,    0.0f,  0.0f, 1.0f,
     0.5f,  0.5f,  0.5f,    0.0f,  0.0f, 1.0f,
    -0.5f,  0.5f,  0.5f,    0.0f,  0.0f, 1.0f,
    -0.5f, -0.5f,  0.5f,    0.0f,  0.0f, 1.0f,

    -0.5f,  0.5f,  0.5f,   -1.0f,  0.0f,  0.0f,
    -0.5f,  0.5f, -0.5f,   -1.0f,  0.0f,  0.0f,
    -0.5f, -0.5f, -0.5f,   -1.0f,  0.0f,  0.0f,
    -0.5f, -0.5f, -0.5f,   -1.0f,  0.0f,  0.0f,
    -0.5f, -0.5f,  0.5f,   -1.0f,  0.0f,  0.0f,
    -0.5f,  0.5f,  0.5f,   -1.0f,  0.0f,  0.0f,

     0.5f,  0.5f,  0.5f,    1.0f,  0.0f,  0.0f,
     0.5f,  0.5f, -0.5f,    1.0f,  0.0f,  0.0f,
     0.5f, -0.5f, -0.5f,    1.0f,  0.0f,  0.0f,
     0.5f, -0.5f, -0.5f,    1.0f,  0.0f,  0.0f,
     0.5f, -0.5f,  0.5f,    1.0f,  0.0f,  0.0f,
     0.5f,  0.5f,  0.5f,    1.0f,  0.0f,  0.0f,

    -0.5f, -0.5f, -0.5f,    0.0f, -1.0f,  0.0f,
     0.5f, -0.5f, -0.5f,    0.0f, -1.0f,  0.0f,
     0.5f, -0.5f,  0.5f,    0.0f, -1.0f,  0.0f,
     0.5f, -0.5f,  0.5f,    0.0f, -1.0f,  0.0f,
    -0.5f, -0.5f,  0.5f,    0.0f, -1.0f,  0.0f,
    -0.5f, -0.5f, -0.5f,    0.0f, -1.0f,  0.0f,

    -0.5f,  0.5f, -0.5f,    0.0f,  1.0f,  0.0f,
     0.5f,  0.5f, -0.5f,    0.0f,  1.0f,  0.0f,
     0.5f,  0.5f,  0.5f,    0.0f,  1.0f,  0.0f,
     0.5f,  0.5f,  0.5f,    0.0f,  1.0f,  0.0f,
    -0.5f,  0.5f,  0.5f,    0.0f,  1.0f,  0.0f,
    -0.5f,  0.5f, -0.5f,    0.0f,  1.0f,  0.0f
};</code></pre>
<p>그냥 이렇게 정점을 정의하고</p>
<pre><code class="language-cpp">// VBO, cubeVAO를 만들고 생성
uint32_t VBO, cubeVAO;
glGenVertexArrays(1, &amp;cubeVAO);
glGenBuffers(1, &amp;VBO);

//VBO에 vertices 데이터 넘기기
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

//color cube VAO 정점 속성 및 인덱스 매핑
glBindVertexArray(cubeVAO);
//정점 속성 매핑
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
//법선 속성 매핑
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);

//light cube VAO 만들기
uint32_t lightCubeVAO;
glGenVertexArrays(1, &amp;lightCubeVAO);
glBindVertexArray(lightCubeVAO);

glBindBuffer(GL_ARRAY_BUFFER, VBO);

//light cube VAO 정점 속성 및 인덱스 매핑
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);</code></pre>
<p>그리고 <code>color.vsh</code>를 수정할 차례!!</p>
<pre><code class="language-cpp">#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;


out vec3 Normal;

void main()
{
    Normal = aNormal;

    gl_Position = projection * view * model * vec4(aPos, 1.0);
}</code></pre>
<h3 id="diffuse계산">diffuse계산</h3>
<p>이제 vertex shader에서는 normal을 받았고 out을 하고있으니
fragment shader에서 색을 입힐 차례!!</p>
<p>그전에!!!</p>
<p>현재 fragment shader에서 fragment의 위치를 알아야
<code>lightDir</code>을 구할 수 있음!</p>
<p><code>lightPos - fragmentPos = lightDir</code>임</p>
<p>그래서 먼저 vertex shader에 out으로 fragPos를 넘겨주도록 하자</p>
<ul>
<li><code>color.vsh</code><pre><code class="language-cpp">#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
</code></pre>
</li>
</ul>
<p>uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;</p>
<p>out vec3 FragPos;
out vec3 Normal;</p>
<p>void main()
{
    FragPos = vec3(model * vec4(aPos, 1.0));
    Normal = aNormal;</p>
<pre><code>gl_Position = projection * view * model * vec4(aPos, 1.0);</code></pre><p>}</p>
<pre><code>
사실 fragment shader로 넘겨서 처리해도 되는데
model변환을 해야해서 vertex shader에서 처리후 넘겨줌

model변환을 해주는 이유는
- 모든 조명은 world좌표계에서 계산됨

때문임
생각해보면 local에서 어떻게 표면을 계산할지도 의문이기도 함ㅋㅋ

이제 fragment shader로 넘어가자~

먼저 out으로 `Normal`, `FragPos`를 넘기고 있으니
in으로 받아주자~

그리고 diffuse공식을 사용하자

diffuse공식은 다시한번 말하지만

- 입사광 방향 벡터 $\cdot$ 법선벡터
   - 모든 벡터는 정규화된 단위 벡터

임

이를 위해서 lightPos를 가져와야됨
따라서 uniform으로 lightPos를 가져오고, 계산~

- `main.cpp` 렌더링 함수 내부
```cpp
normalCubeShader.setVec3(&quot;lightPos&quot;, lightPos);</code></pre><ul>
<li><code>color.fsh</code><pre><code class="language-cpp">#version 330 core
out vec4 FragColor;
</code></pre>
</li>
</ul>
<p>in vec3 Normal;
in vec3 FragPos;</p>
<p>uniform vec3 objectColor;
uniform vec3 lightColor;
uniform vec3 lightPos;</p>
<p>void main()
{
    //ambient
    float ambientStrength = 0.1f;
    vec3 ambient = ambientStrength * lightColor;</p>
<pre><code>//diffuse
vec3 norm = normalize(Normal);
vec3 lightDir = normalize(lightPos - FragPos);
float diff = max(dot(norm, lightDir), 0.0);
vec3 diffuse = diff * lightColor;

//result
vec3 color = objectColor * (ambient + diffuse);
FragColor = vec4(color, 1.0);</code></pre><p>}</p>
<pre><code>
![](https://velog.velcdn.com/images/vfx_master/post/d33fafbb-356c-4ae9-b5b8-82dd5ac7a0c1/image.png)

이런 diffuse가 만들어짐 구웃~

여기서 중요한점 하나

### optional. 법선 행렬(법선벡터 변환)

법선벡터는 현재 표면에 대해서만 유효함

뭔소린가 하믄...

![](https://velog.velcdn.com/images/vfx_master/post/e74fda88-1105-4c5a-b51c-fa5ead057452/image.png)

이 사진을 봐보자

변환행렬을 통해 scale을 바꿀 수 있다는걸 지난번에 알아봤음

법선 벡터는 scale이 일정하게 늘거나 줄어들을 때, 
즉 1대1 비율일때, 법선은 일정하게 유지되지만,
표면의 비율이 1대1로 scale이 변환되지 않으면 법선은 90$\degree$를 유지하지 않음

이때 법선벡터를 world좌표로 변환시켜주는 함수(행렬)을 
**법선행렬**이라고 함

- [법선 벡터의 변환을 위한 법선 행렬](http://www.gisdeveloper.co.kr/?p=2224) - GIS Developer

이분의 수식 설명을 보고 이해해보도록 하자

식은 간단함

`model`행렬이 local좌표에서 world좌표로 변환시키는 행렬이라는걸 알고있을거임

그럼 `model`행렬의 역행렬의 전치 행렬이 법선 행렬(MN)이 되어서,
`MN * Normal`을 하면 어떠한 scale변화에도 normal이 유지됨

- 역행렬 : 행렬 $M$ \*역행렬 $M^{-1}$의 곱은 단위행렬 $I$
- 전치 행렬 : 행렬 $M$의 행과 열을 바꾼 행렬

이거는 쉽게 shader에서 사용할 수 있는데,,,
문제는 역행렬은 약 $O(N^3)$의 시간복잡도를 가진다는거임

그래서 되도록이면 쉐이더에서 매 프레임 계산하기보다는...
가능하다면 CPU에서...

1. model좌표가 변하지 않으면 역행렬도 유지
2. model좌표가 변할때만 역행렬 재연산

와 같이 최적화하자~~

귀찮으니 쉐이더에서 일단 함..

- `color.vsh`
```cpp
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;


out vec3 FragPos;
out vec3 Normal;

void main()
{
    FragPos = vec3(model * vec4(aPos, 1.0));
    Normal = mat3(transpose(inverse(model))) * aNormal;

    gl_Position = projection * view * model * vec4(aPos, 1.0);
}</code></pre><p>Normal이 바뀜~~</p>
<h2 id="specular">specular</h2>
<p>이제 정점인
하이라이트 반사광을 추가하는거임</p>
<p>입사광이 표면에서 반사되었을때의 벡터와
시점(카메라) 사이의 각도를 계산해서
1과 가까울때(닮을때) 가장 많은 빛을 반사하고,
그게 아니라면 서서히 감소(falloff)를 시키면 됨</p>
<p>공식도 개간단함</p>
<blockquote>
</blockquote>
<p>SpecularStrength = $S_s$
view Direction = $V$
reflected vector = $R$
fall off = $F$</p>
<blockquote>
</blockquote>
<p>$(|V| \cdot |R|)^F * S_s * lightColor$</p>
<p>임ㅋㅋ</p>
<blockquote>
<p><strong>world변환 VS view변환</strong></p>
</blockquote>
<p>위에서 diffuse를 우리는 world에서 수행했음
근데 view에서 하는게 더 간편함</p>
<blockquote>
</blockquote>
<p>view에서는 시점이 무조건 0,0,0으로 고정임</p>
<blockquote>
</blockquote>
<p>따라서 model + view 변환 행렬까지 사용하여
<code>FragmentPos</code>와 <code>Normal</code>을 계산하여 사용하는게
더 적은 자원으로 같은 효과를 낼 수 있는거지비</p>
<p>먼저
<code>viewPos</code>를 uniform을 통해 입력받음</p>
<ul>
<li><p><code>color.fsv</code></p>
<pre><code class="language-cpp">uniform vec3 viewPos;</code></pre>
</li>
<li><p><code>main.cpp</code></p>
<pre><code class="language-cpp">normalCubeShader.setVec3(&quot;viewPos&quot;, camera.Position);</code></pre>
</li>
</ul>
<p>그리고 이걸 통해서 specular를 계산하면됨</p>
<p>specular는 간단함</p>
<pre><code class="language-cpp">//specular
float specularStrength = 0.8f;
vec3 viewDir = normalize(viewPos - FragPos);
vec3 reflectDir = reflect(-lightDir, norm); 
float spec = pow(max(dot(viewDir, reflectDir), 0.0), 256);
vec3 specular = specularStrength * spec * lightColor;</code></pre>
<p>이렇게 계산이 됨</p>
<p>pow를 통해 지수를 늘려주면
시점을 제외한 부분에서 급격한 빛 반사 감소가 일어나서 부자연스러워 질수도 있음</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/7e50c3c0-bad2-40d1-9a32-10c68da968f3/image.png" /></p>
<p>적당히 알아서 원하는 값으로 고고</p>
<p>난 확실한 차이를 보고싶어서 256처럼 큰값으로 사용함</p>
<ul>
<li><code>color.fsv</code><pre><code class="language-cpp">#version 330 core
out vec4 FragColor;
</code></pre>
</li>
</ul>
<p>in vec3 Normal;
in vec3 FragPos;</p>
<p>uniform vec3 objectColor;
uniform vec3 lightColor;
uniform vec3 lightPos;
uniform vec3 viewPos;</p>
<p>void main()
{
    //ambient
    float ambientStrength = 0.1f;
    vec3 ambient = ambientStrength * lightColor;</p>
<pre><code>//diffuse
vec3 norm = normalize(Normal);
vec3 lightDir = normalize(lightPos - FragPos);
float diff = max(dot(norm, lightDir), 0.0);
vec3 diffuse = diff * lightColor;

//specular
float specularStrength = 0.8f;
vec3 viewDir = normalize(viewPos - FragPos);
vec3 reflectDir = reflect(-lightDir, norm); 
float spec = pow(max(dot(viewDir, reflectDir), 0.0), 256);
vec3 specular = specularStrength * spec * lightColor;

//result
vec3 color = objectColor * (ambient + diffuse + specular);
FragColor = vec4(color, 1.0);</code></pre><p>}</p>
<pre><code>
개간단!!
![](https://velog.velcdn.com/images/vfx_master/post/3441fc3f-d41c-4349-a813-59569394d3f6/image.gif)

### view변환을 통해 쉐이딩

- `color.vsh`
```cpp
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;

out vec3 FragPos;
out vec3 Normal;
out vec3 LightPos;

uniform vec3 lightPos; //view변환

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{
    gl_Position = projection * view * model * vec4(aPos, 1.0);
    FragPos = vec3(view * model * vec4(aPos, 1.0));
    Normal = mat3(transpose(inverse(view * model))) * aNormal;
    LightPos = vec3(view * vec4(lightPos, 1.0)); 
}</code></pre><ul>
<li><code>color.fsh</code><pre><code class="language-cpp">#version 330 core
out vec4 FragColor;
</code></pre>
</li>
</ul>
<p>in vec3 FragPos;
in vec3 Normal;
in vec3 LightPos; </p>
<p>uniform vec3 lightColor;
uniform vec3 objectColor;</p>
<p>void main()
{
    // ambient
    float ambientStrength = 0.1;
    vec3 ambient = ambientStrength * lightColor;</p>
<pre><code>// diffuse 
vec3 norm = normalize(Normal);
vec3 lightDir = normalize(LightPos - FragPos);
float diff = max(dot(norm, lightDir), 0.0);
vec3 diffuse = diff * lightColor;

// specular
float specularStrength = 0.8;
vec3 viewDir = normalize(-FragPos); //원점 - FragPos
vec3 reflectDir = reflect(-lightDir, norm);
float spec = pow(max(dot(viewDir, reflectDir), 0.0), 256);
vec3 specular = specularStrength * spec * lightColor;

vec3 result = (ambient + diffuse + specular) * objectColor;
FragColor = vec4(result, 1.0);</code></pre><p>}</p>
<pre><code>
# 플랫 쉐이딩 vs 구로 쉐이딩 vs 퐁 쉐이딩

학부에서 그래픽스를 공부할때 플랫 쉐이딩 &amp; 구로 쉐이딩을 공부했음

flat이나 gouraud나 phong이나 똑같음

## flat shading
flat은 면 단위로 쉐이딩을 함
즉 같은 면이면 같은 색상을 칠하는거임

![](https://velog.velcdn.com/images/vfx_master/post/af444ebf-353c-4559-b382-bf930438861e/image.png)

이렇게 로우폴리 느낌이 날때 사용하면 좋음
혹은 각진 사물이라던지 ㅇㅇ

## gouraud shading

구로 쉐이딩이라고 하는데
위 플랫 쉐이딩은 면마다 색상이 결정되므로 부자연스럽다는 문제가 있음

그래서 각 메쉬의 정점단위로 색상을 결정짓기 시작함

기본적으로 메쉬는 삼각형으로 이루어져 있음

이때 각 정점을 둘러싸고 있는 면의 normal의 합의 평균의 단위벡터를 구해서 그걸로 shading하는거임

![](https://velog.velcdn.com/images/vfx_master/post/dc608091-3eb8-49e5-aa22-3dd3338196ba/image.png)

이거임

근데 정점인데 어떻게 표면에서 색상이 결정되는거지?

이게 핵심인데
`geometry shader`에서 자동적으로 정점 사이를 보간처리함

그래서 정점이 만든 삼각형 메쉬 사이를 
vertex shader에서 정점에 쌓인 색상 데이터를 이용해
보간을 하여 모든 평면의 픽셀에 대해 색상을 결정지음

## phong shading

구로 쉐이딩은 문제가 있음

1. 마하 밴딩
메쉬를 기본적인 단위는 삼각형임
따라서 삼각형의 크기가 커지면, 선형보간을 통해 점, 선, 면의 색상이 결정되고,
이로 인해 조명의 강도가 변하는 지점에서 메쉬 단위가 눈에 보이게 되는 문제
&lt;img src=&quot;https://velog.velcdn.com/images/vfx_master/post/9e644723-bf2b-40e9-92d5-e017423ad695/image.png&quot; width = 50%/&gt;
2. 하이라이트
위에서 살펴봤듯이 specular로 하이라이트를 표현함
근데 정점 단위로 계산을 하게 되면, 
하이라이트가 필요한 표면에 대해서는 계산이 이뤄지지 않고, 단순히 보간을 통해서 색상만 결정되므로, 하이라이트 표현이 힘들어짐
&lt;img src=&quot;https://velog.velcdn.com/images/vfx_master/post/ef7e3c2f-8daa-4010-8a3f-465742c06190/image.gif&quot; width = 50%/&gt;

뭐 이런저런 문제가 있어서 등장한게 
phong shading임

다 똑같은데 fragment shader로 넘어온 원시 삼각형의 표면에 대해서 
light shading을 하는거임

표면은 보간을 통해 자연스럽게 픽셀의 좌표가 결정된 상태이므로
하이라이트도 표면단위로 되고, 색상 계산은 표면단위로 계산되므로 조명이 어떻게 변화하든 메쉬단위가 보이지 않게 됨
</code></pre>