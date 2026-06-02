<p><a href="https://velog.io/@vfx_master/%EB%82%98%EB%A7%8C%EC%9D%98-tiny-renderer-%EB%A7%8C%EB%93%A4%EA%B8%B0-4-Z-Buffer-%EC%95%A0%ED%8C%8C%EC%9D%B8%EC%95%84%ED%95%80-%EB%B3%80%ED%99%98#affine-transformation">나만의 tiny renderer 만들기 (4) - Z-Buffer, 애파인(아핀) 변환</a></p>
<p>여기서 아핀 변환쪽을 잠깐 이해하는 시간을 가져보도록 하자</p>
<p>행렬은 벡터에 적용되는 함수정도라고 생각하고 보면 이해될듯?</p>
<p>여기에 추가로 오일러 각 + 쿼터니언 개념 가지고 가면 좋음</p>
<h1 id="오일러-각">오일러 각</h1>
<p>3차원에서 물체의 회전을 x,y,z축 각각의 회전으로 보는거임</p>
<p>x,y,z축의 회전을 순서대로 각각 roll, pitch, yaw회전이라고 함</p>
<p>이때 각 축의 회전하는 함수(행렬)은 다음과 같음</p>
<ul>
<li><p>X축 기준 회전 행렬
$$R_x(\theta) = \begin{pmatrix} 1 &amp; 0 &amp; 0 \ 0 &amp; \cos\theta &amp; -\sin\theta \ 0 &amp; \sin\theta &amp; \cos\theta \end{pmatrix} \cdot \begin{bmatrix}x\y\z\1 \end{bmatrix} = \begin{pmatrix} x \ cosθ⋅y−sinθ⋅z\sinθ⋅y+cosθ⋅z \1\end{pmatrix}$$</p>
</li>
<li><p>Y축 기준 회전 행렬
$$R_y(\theta) = \begin{pmatrix} \cos\theta &amp; 0 &amp; \sin\theta \ 0 &amp; 1 &amp; 0 \ -\sin\theta &amp; 0 &amp; \cos\theta \end{pmatrix}\cdot \begin{bmatrix}x\y\z\1 \end{bmatrix} = \begin{pmatrix} cosθ⋅x+sinθ⋅z\y\−sinθ⋅x+cosθ⋅z \1\end{pmatrix}$$</p>
</li>
<li><p>Z축 기준 회전 행렬
$$R_z(\theta) = \begin{pmatrix} \cos\theta &amp; -\sin\theta &amp; 0 \ \sin\theta &amp; \cos\theta &amp; 0 \ 0 &amp; 0 &amp; 1 \end{pmatrix}\cdot \begin{bmatrix}x\y\z\1 \end{bmatrix} = \begin{pmatrix} cosθ⋅x−sinθ⋅y\sinθ⋅x+cosθ⋅y\z \1\end{pmatrix}$$</p>
</li>
</ul>
<p>가만히 보면 완벽한 회전일거같은데 2개의 문제를 가짐</p>
<h2 id="보간시-곡선유지가-안됨">보간시 곡선유지가 안됨</h2>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/a597d7ef-248c-4b2e-a941-d25eea03e596/image.png" /></p>
<p>이렇게 두 점을 $\theta_x = 45\degree$, $\theta_y = 67.5\degree$, $\theta_z = 45\degree$만큼씩 움직인다고 생각해보자</p>
<h3 id="theta_x-이동">$\theta_x$ 이동</h3>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/186603d9-b603-4d88-a867-478fcd23f485/image.png" /></p>
<h3 id="theta_y-이동">$\theta_y$ 이동</h3>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/dbee25f1-0624-413e-9b17-19994385f958/image.png" /></p>
<h3 id="theta_z-이동">$\theta_z$ 이동</h3>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/e54dc6fc-32b8-49f2-807c-876477c13706/image.png" /></p>
<hr />
<p>즉 각 벡터의 이동경로만 따지고 보면</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/15e4fc41-42c8-4992-815f-9c13df20c5f9/image.png" /></p>
<p>이런 괴상한 경로가 만들어지는거임</p>
<p>이렇게 되는 이유는 간단한데</p>
<p>x,y,z를 따로따로, 각각, 한개씩 회전을 해서 그럼</p>
<h2 id="짐벌락">짐벌락</h2>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/d5fe895a-a9e7-426c-a97f-b72e659a181b/image.gif" /></p>
<p>이 gif를 보자</p>
<p>첨엔 제대로 회전하다가 나중에는 x,y,z의 회전축이 겹쳐서
어느순간에는 어떤 축이 회전하는지 판단이 불가능한 순간이 옴</p>
<p>예를들어 z축으로 90도를 회전시키면
x축과 y축이 같은 방향을 가리키게 됨</p>
<p>이때, x축을 회전하는것과 y축을 회전하는것을 비교할 수 있을까?
어떤게 회전하고 있는걸까?</p>
<p>이러한 축 겹침은 x,y | y,z | x,z | x,y,z와 같이 모든 회전축에 대해 일어날 수 있음</p>
<p>이렇게 보간과 짐벌락 현상을 해결하기 위해 등장한 개념이</p>
<h1 id="쿼터니언사원수">쿼터니언(사원수)</h1>
<p>임</p>
<p>뭐 어떻게 생기고, 어떤 공식이 사용되고 이런거는 좀 영역밖의 일이라서 간단하게 원리를 설명하면</p>
<p>오일러각은 x,y,z축에 대하여 각각 회전을 주는 방식이었다면</p>
<p>쿼터니언은</p>
<ol>
<li>축의 회전순서가 정해져 있지 않음</li>
<li>선형보간이 아닌 구면 선형보간(Slerp)를 이용해서 자연스러운 곡선 유도</li>
<li>slerp를 통한 회전에 필요한 가장 최단경로 유도<ul>
<li>현재 x축이 0도일때 330도로 회전해야한다면<ul>
<li>오일러각은 +330도를 움직임</li>
<li>쿼터니언은 -30도를 움직임</li>
</ul>
</li>
</ul>
</li>
<li>회전축(x,y,z)와 회전각($\theta$)를 이용함<ul>
<li>쿼터니언에서 x,y,z,w를 사용하는데 w는 회전각의 절반인 $\frac{\theta}{2}$임</li>
</ul>
</li>
</ol>
<p>즉, 쿼터니언은
축 하나씩 따로 회전하지 않고
전체의 회전정보를 $x,y,z,w$에 담아 한번에 회전시키는거임</p>
<h1 id="glm">glm</h1>
<p>glm이라는 라이브러리를 사용하면 행렬, 벡터, 변환 모두 자동으로 사용가능!!</p>
<p><a href="https://glm.g-truc.net/0.9.8/index.html">https://glm.g-truc.net/0.9.8/index.html</a></p>
<p>이 라이브러리를 includes폴더로 이동시키면 됨</p>
<h2 id="이동">이동</h2>
<pre><code class="language-cpp">#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;
#include &lt;glm/gtc/type_ptr.hpp&gt;

glm::vec4 vec(1.0f, 0.0f, 0.0f, 1.0f);
glm::mat4 trans = glm::mat4(1.0f);
trans = glm::translate(trans, glm::vec3(1.0f, 1.0f, 0.0f));
vec = trans * vec;
std::cout &lt;&lt; vec.x &lt;&lt; vec.y &lt;&lt; vec.z &lt;&lt; std::endl;</code></pre>
<p>알고보면 간단함</p>
<p>자자 위에서 행렬을 뭐라고 했지?</p>
<blockquote>
<p>행렬은 벡터 변환 전용 함수다</p>
</blockquote>
<p>그럼 이 코드를 해석해보자</p>
<ul>
<li><code>1,0,0,1</code>이라는 <code>vec4</code>를 만듬</li>
<li><code>trans</code>라는 4*4의 단위행렬을 만듬<ul>
<li>단위행렬이 아니고 영행렬이면, 모든 값이 nullptr이 됨</li>
</ul>
</li>
<li><code>translate</code>(변환)메서드를 이용해서 <code>1,1,0</code>만큼 이동하는 변환 행렬을 만듬</li>
<li>변환행렬과 원본 벡터를 곱해서 x로 1, y로 1만큼 움직인 벡터를 만들음</li>
</ul>
<p>졸라쉬움!</p>
<h2 id="회전">회전</h2>
<pre><code class="language-cpp">#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;
#include &lt;glm/gtc/type_ptr.hpp&gt;

glm::vec4 vec(1.0f, 0.0f, 0.0f, 1.0f);
glm::mat4 trans = glm::mat4(1.0f);
trans = glm::rotate(trans, glm::radians(60.0f), glm::vec3(/*회전축*/0, 0, 1));
vec = trans * vec;
std::cout &lt;&lt; vec.x &lt;&lt; vec.y &lt;&lt; vec.z &lt;&lt; std::endl;</code></pre>
<p>이것도 쉬움</p>
<ul>
<li><code>vec4</code>로 원본좌표 표시</li>
<li>4*4 단위행렬 만들기</li>
<li>변환 행렬로 만들어주기<ul>
<li>첫번째 : 변환에 사용할 행렬</li>
<li>두번째 : 회전할 각도, 
<code>glm::radians</code>를 이용해 60분법이라 불리는 일상의 각도를 라디안으로 만들어주기($\theta$임 이게 ㅇㅇ)</li>
<li>세번째 : 회전축
중요한건, x축, y축, z축을 중심으로 회전하는게 아니면, 회전축에 해당되는 벡터를 정규화를 통해 단위 벡터로 만들어 사용해야함</li>
</ul>
</li>
<li>변환행렬에 원본 벡터를 곱해 변환시키기</li>
</ul>
<h2 id="크기-조절">크기 조절</h2>
<pre><code class="language-cpp">#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;
#include &lt;glm/gtc/type_ptr.hpp&gt;

glm::vec4 vec(1.0f, 0.0f, 0.0f, 1.0f);
glm::mat4 trans = glm::mat4(1.0f);
trans = glm::scale(trans, glm::vec3(0.8, 0.2, 1));
vec = trans * vec;
std::cout &lt;&lt; vec.x &lt;&lt; vec.y &lt;&lt; vec.z &lt;&lt; std::endl;</code></pre>
<p>이것도 졸라쉬워</p>
<ul>
<li>원본 벡터 </li>
<li>변환용 단위 행렬</li>
<li><code>scale</code>메서드를 이용해서 원하는 크기를 설정하는 변환행렬로 만들기</li>
<li>변환행렬에 원본 벡터를 곱해서 변환시키기</li>
</ul>
<p>쉬움!!</p>
<h2 id="다같이-사용">다같이 사용</h2>
<pre><code class="language-cpp">#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;
#include &lt;glm/gtc/type_ptr.hpp&gt;

glm::vec4 vec(1.0f, 0.0f, 0.0f, 1.0f);
glm::mat4 trans = glm::mat4(1.0f);
trans = glm::translate(trans, glm::vec3(0.1f, 0.4f, -0.5f)); 
trans = glm::rotate(trans, glm::radians(60.0f), glm::vec3(/*회전축*/0, 0, 1));
trans = glm::scale(trans, glm::vec3(0.5f, 0.5f, 0.2f));
vec = trans * vec;
std::cout &lt;&lt; vec.x &lt;&lt; vec.y &lt;&lt; vec.z &lt;&lt; std::endl;</code></pre>
<p>그냥 변환용 행렬을 원하는 함수로 만들어주면 되는거임</p>
<h2 id="쉐이더에서-사용">쉐이더에서 사용</h2>
<p>glsl에도 matrix 자료형이 있음</p>
<p><code>mat4</code>임</p>
<pre><code class="language-cpp">#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 2) in vec2 aTexCoord;

out vec2 TexCoord;

uniform mat4 transform;

void main()
{
    gl_Position = transform * vec4(aPos, 1.0f);
    TexCoord = vec2(aTexCoord.x, aTexCoord.y);
} </code></pre>
<p>vertex shader를 위처럼 수정해주고</p>
<pre><code class="language-cpp">// 변환행렬
glm::mat4 transform = glm::mat4(1.0f); // make sure to initialize matrix to identity matrix first
transform = glm::translate(transform, glm::vec3(0.5f, -0.5f, 0.0f));
transform = glm::rotate(transform, (float)glfwGetTime(), glm::vec3(0.0f, 0.0f, 1.0f));
transform = glm::scale(transform, glm::vec3(0.5f, 0.5f, 0.5f));

// 쉐이더 사용
shaderProgram.useShader();
unsigned int transformLoc = glGetUniformLocation(shaderProgram.shaderProgramID, &quot;transform&quot;);
glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(transform));</code></pre>
<p>이런식으로 쓰면 되는데
<code>glUniformMatrix4fv</code>이걸 설명해줌</p>
<ul>
<li>첫번째 : uniform 변수 위치</li>
<li>두번째 : gl에게 보낼 행렬의 개수</li>
<li>세번째 : 행렬에서 행과 열을 swap할지</li>
<li>네번째 : 보낼 행렬<ul>
<li>다만 glm의 행렬은 gl에서 원하는 행렬과 조금은 다른 모습이다
따라서 <code>glm::value_ptr</code>을 이용해 행렬의 값만 넘겨주도록 하자</li>
</ul>
</li>
</ul>
<h2 id="전체코드">전체코드</h2>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;
#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;
#include &lt;glm/gtc/type_ptr.hpp&gt;

#include &quot;Shader.h&quot;
#include &quot;stb_image.h&quot;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void process_input(GLFWwindow *window);

float vertices[] = 
{
    //positions            //colors             //텍스쳐 좌표 
     0.5f,  0.5f, 0.0f,    1.0f, 0.0f, 0.0f,    1.0f, 1.0f,  //우상단
     0.5f, -0.5f, 0.0f,    0.0f, 1.0f, 0.0f,    1.0f, 0.0f,  //우하단
    -0.5f, -0.5f, 0.0f,    0.0f, 0.0f, 1.0f,    0.0f, 0.0f,  //좌하단
    -0.5f,  0.5f, 0.0f,    1.0f, 1.0f, 0.0f,    0.0f, 1.0f   //좌상단
};

uint32_t indices[] = 
{
    0, 1, 3,
    1, 2, 3
};

float lerp = 0.0f;

int main()
{
    constexpr int windowWidth = 1280;
    constexpr int windowHeight = 720;

    //glfw 초기화
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    //glfw로 창 만들기
    GLFWwindow* window = glfwCreateWindow(windowWidth, windowHeight, &quot;HukPak&quot;, nullptr, nullptr);
    if (window == NULL)
    {
        std::cout &lt;&lt; &quot;Failed to create GLFW window&quot; &lt;&lt; std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);

    //GLAD를 사용해 Gl함수들의 포인터를 체킹
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
    {
        std::cout &lt;&lt; &quot;Failed to initialize GLAD&quot; &lt;&lt; std::endl;
        return -1;
    }

    //커스텀 콜백 메서드 framebuffer_size_callback를 이용해 glfw의 창 크기 설정
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    // 쉐이더 생성
    Shader shaderProgram(
        &quot;D:/01_Develope/CPP/learn_opengl/learn_opengl/VertexShaderSource.vsh&quot;, 
        &quot;D:/01_Develope/CPP/learn_opengl/learn_opengl/FragmentShaderSource.fsh&quot;);

    // VAO
    uint32_t VAO;
    glGenVertexArrays(1, &amp;VAO);
    glBindVertexArray(VAO);

    // VBO
    uint32_t VBO;
    glGenBuffers(1, &amp;VBO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    // EBO
    uint32_t EBO;
    glGenBuffers(1, &amp;EBO);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

    // 버텍스 속성 링킹
    //position
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)0);
    glEnableVertexAttribArray(0);
    //color
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)(sizeof(float) * 3));
    glEnableVertexAttribArray(1);
    //텍스쳐 버텍스
    glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)(sizeof(float) * 6));
    glEnableVertexAttribArray(2);


    // GL에게 버텍스 매핑은 끝이므로, 더이상 바인딩 안한다고 명시적으로 알림
    glBindVertexArray(0);

    //텍스쳐 만들기
    uint32_t texture1, texture2;
    glGenTextures(1, &amp;texture1);
    glBindTexture(GL_TEXTURE_2D, texture1);

    //텍스쳐 크기를 넘어가는 버텍스에 대해선 가로/세로 모두 repeat
    //텍스쳐가 축소되면 mipmap linear 축소, 텍스쳐가 확대되면 linear 확대
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);    
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

    //텍스쳐 이미지 로딩
    int imageWidth, imageHeight, nrChannels;
    stbi_set_flip_vertically_on_load(true); 
    uint8_t *data = stbi_load(&quot;wall.jpg&quot;, &amp;imageWidth, &amp;imageHeight, &amp;nrChannels, 0);

    //텍스쳐 이미지가 로딩 성공하면 해당 이미지로 텍스쳐Image2D 생성, mipmap생성
    if (data)
    {
        glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, imageWidth, imageHeight, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
        glGenerateMipmap(GL_TEXTURE_2D);
    }
    else
    {
        std::cout &lt;&lt; &quot;Failed to load Texture file&quot; &lt;&lt; std::endl;
    }

    //로드된 이미지 메모리 해제
    stbi_image_free(data);

    //2번 텍스쳐 반복
    glGenTextures(1, &amp;texture2);
    glBindTexture(GL_TEXTURE_2D, texture2);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);    
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    //
    data = stbi_load(&quot;awesomeface.png&quot;, &amp;imageWidth, &amp;imageHeight, &amp;nrChannels, 0);
    if (data) //png이므로 RGBA채널 사용
    {
        glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, imageWidth, imageHeight, 0, GL_RGBA, GL_UNSIGNED_BYTE, data);
        glGenerateMipmap(GL_TEXTURE_2D);
    }
    else
    {
        std::cout &lt;&lt; &quot;Failed to load Texture file&quot; &lt;&lt; std::endl;
    }
    stbi_image_free(data);

    //uniform사용하기 위해 shader program 미리 use해주기
    shaderProgram.useShader();
    glUniform1i(glGetUniformLocation(shaderProgram.shaderProgramID, &quot;tex1&quot;), 0);
    glUniform1i(glGetUniformLocation(shaderProgram.shaderProgramID, &quot;tex2&quot;), 1);

    //run window
    while(!glfwWindowShouldClose(window))
    {
        process_input(window);

        glClearColor(.0f, .0f, .0f, 1.f);
        glClear(GL_COLOR_BUFFER_BIT);

        //텍스쳐 유닛 사용
        glActiveTexture(GL_TEXTURE0); //0번 채널
        glBindTexture(GL_TEXTURE_2D, texture1);
        glActiveTexture(GL_TEXTURE1); //1번 채널
        glBindTexture(GL_TEXTURE_2D, texture2);

        // 변환행렬
        glm::mat4 transform = glm::mat4(1.0f); // make sure to initialize matrix to identity matrix first
        transform = glm::translate(transform, glm::vec3(0.5f, -0.5f, 0.0f));
        transform = glm::rotate(transform, (float)glfwGetTime(), glm::vec3(0.0f, 0.0f, 1.0f));
        transform = glm::scale(transform, glm::vec3(0.5f, 0.5f, 0.5f));

        // 쉐이더 사용
        shaderProgram.useShader();
        unsigned int transformLoc = glGetUniformLocation(shaderProgram.shaderProgramID, &quot;transform&quot;);
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(transform));

        //VAO 활성화
        glBindVertexArray(VAO);
        // 그리기
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
        //VAO비활성화
        glBindVertexArray(0);

        //swapping buffer frame, and execute event as like input or something
        glfwSwapBuffers(window);
        glfwPollEvents();    
    }

    //optional, don't need it but keep do it until used to OpenGL
    glDeleteBuffers(1, &amp;VBO);
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

    if (glfwGetKey(window, GLFW_KEY_UP) == GLFW_PRESS)
    {
        if (lerp &gt;= 0.9f) return;
        lerp += 0.01f;
    }
    else if (glfwGetKey(window, GLFW_KEY_DOWN) == GLFW_PRESS)
    {
        if (lerp &lt;= 0.1f) return;
        lerp -= 0.01f;
    }
}</code></pre>
<p>이렇게 while문 내부에 코드를 작성하면...</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/9a2b8019-311c-405e-8889-809b66f864ab/image.gif" /></p>
<p>혹은 회전을 약간 다르게 해볼까?</p>
<ol>
<li>회전</li>
<li>이동</li>
<li>스케일</li>
</ol>
<p>이런식으로 변환 행렬을 만들면??</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/c3d72dcd-2b3d-424a-ac95-3d84ce421091/image.gif" /></p>
<p>이렇게 되는 이유는 간단함</p>
<ul>
<li>회전 행렬을 만들때의 좌표는 <code>0,0,0</code>이었음</li>
<li>여기서 이동행렬로 이동만 시켜버리니 회전은 <code>0,0,0</code>을 기준으로 수행되는데
이동은 그 이후</li>
<li>스케일은 이동후에 적용되므로, 기본 크기일때는 좌상단 꼭지점을 기준으로 회전하는데,
그 스케일을 줄여버리니 저렇게 요상하게 돌아가버리는겨~</li>
</ul>
<p>이렇게 언제 회전하고, 언제 이동하고, 언제 scale을 조정하냐에 따라 변환은 신기하게 바뀜ㅋㅋㅋ</p>
<h3 id="부가">부가</h3>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;
#include &lt;random&gt;
#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;
#include &lt;glm/gtc/type_ptr.hpp&gt;

#include &quot;Shader.h&quot;
#include &quot;stb_image.h&quot;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void process_input(GLFWwindow *window);

float vertices[] = 
{
    //positions            //colors             //텍스쳐 좌표 
     0.5f,  0.5f, 0.0f,    1.0f, 0.0f, 0.0f,    1.0f, 1.0f,  //우상단
     0.5f, -0.5f, 0.0f,    0.0f, 1.0f, 0.0f,    1.0f, 0.0f,  //우하단
    -0.5f, -0.5f, 0.0f,    0.0f, 0.0f, 1.0f,    0.0f, 0.0f,  //좌하단
    -0.5f,  0.5f, 0.0f,    1.0f, 1.0f, 0.0f,    0.0f, 1.0f   //좌상단
};

uint32_t indices[] = 
{
    0, 1, 3,
    1, 2, 3
};

float lerp = 0.0f;

int main()
{
    constexpr int windowWidth = 1280;
    constexpr int windowHeight = 720;

    //glfw 초기화
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    //glfw로 창 만들기
    GLFWwindow* window = glfwCreateWindow(windowWidth, windowHeight, &quot;HukPak&quot;, nullptr, nullptr);
    if (window == NULL)
    {
        std::cout &lt;&lt; &quot;Failed to create GLFW window&quot; &lt;&lt; std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);

    //GLAD를 사용해 Gl함수들의 포인터를 체킹
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
    {
        std::cout &lt;&lt; &quot;Failed to initialize GLAD&quot; &lt;&lt; std::endl;
        return -1;
    }

    //커스텀 콜백 메서드 framebuffer_size_callback를 이용해 glfw의 창 크기 설정
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    // 쉐이더 생성
    Shader shaderProgram(
        &quot;D:/01_Develope/CPP/learn_opengl/learn_opengl/VertexShaderSource.vsh&quot;, 
        &quot;D:/01_Develope/CPP/learn_opengl/learn_opengl/FragmentShaderSource.fsh&quot;);

    // VAO
    uint32_t VAO;
    glGenVertexArrays(1, &amp;VAO);
    glBindVertexArray(VAO);

    // VBO
    uint32_t VBO;
    glGenBuffers(1, &amp;VBO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    // EBO
    uint32_t EBO;
    glGenBuffers(1, &amp;EBO);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

    // 버텍스 속성 링킹
    //position
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)0);
    glEnableVertexAttribArray(0);
    //color
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)(sizeof(float) * 3));
    glEnableVertexAttribArray(1);
    //텍스쳐 버텍스
    glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)(sizeof(float) * 6));
    glEnableVertexAttribArray(2);


    // GL에게 버텍스 매핑은 끝이므로, 더이상 바인딩 안한다고 명시적으로 알림
    glBindVertexArray(0);

    //텍스쳐 만들기
    uint32_t texture1, texture2;
    glGenTextures(1, &amp;texture1);
    glBindTexture(GL_TEXTURE_2D, texture1);

    //텍스쳐 크기를 넘어가는 버텍스에 대해선 가로/세로 모두 repeat
    //텍스쳐가 축소되면 mipmap linear 축소, 텍스쳐가 확대되면 linear 확대
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);    
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

    //텍스쳐 이미지 로딩
    int imageWidth, imageHeight, nrChannels;
    stbi_set_flip_vertically_on_load(true); 
    uint8_t *data = stbi_load(&quot;wall.jpg&quot;, &amp;imageWidth, &amp;imageHeight, &amp;nrChannels, 0);

    //텍스쳐 이미지가 로딩 성공하면 해당 이미지로 텍스쳐Image2D 생성, mipmap생성
    if (data)
    {
        glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, imageWidth, imageHeight, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
        glGenerateMipmap(GL_TEXTURE_2D);
    }
    else
    {
        std::cout &lt;&lt; &quot;Failed to load Texture file&quot; &lt;&lt; std::endl;
    }

    //로드된 이미지 메모리 해제
    stbi_image_free(data);

    //2번 텍스쳐 반복
    glGenTextures(1, &amp;texture2);
    glBindTexture(GL_TEXTURE_2D, texture2);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);    
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    //
    data = stbi_load(&quot;awesomeface.png&quot;, &amp;imageWidth, &amp;imageHeight, &amp;nrChannels, 0);
    if (data) //png이므로 RGBA채널 사용
    {
        glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, imageWidth, imageHeight, 0, GL_RGBA, GL_UNSIGNED_BYTE, data);
        glGenerateMipmap(GL_TEXTURE_2D);
    }
    else
    {
        std::cout &lt;&lt; &quot;Failed to load Texture file&quot; &lt;&lt; std::endl;
    }
    stbi_image_free(data);

    //uniform사용하기 위해 shader program 미리 use해주기
    shaderProgram.useShader();
    glUniform1i(glGetUniformLocation(shaderProgram.shaderProgramID, &quot;tex1&quot;), 0);
    glUniform1i(glGetUniformLocation(shaderProgram.shaderProgramID, &quot;tex2&quot;), 1);

    std::random_device rd;  // 하드웨어 기반 시드 생성
    std::mt19937 gen(rd());
    std::uniform_int_distribution&lt;int&gt; distrib(-100, 100);
    float rand1 = static_cast&lt;float&gt;(distrib(gen)) / 100;
    float rand2 = static_cast&lt;float&gt;(distrib(gen)) / 100;
    float rand3 = static_cast&lt;float&gt;(distrib(gen)) / 100;
    float rand4 = static_cast&lt;float&gt;(distrib(gen)) / 100;

    //run window
    while(!glfwWindowShouldClose(window))
    {
        process_input(window);

        glClearColor(.0f, .0f, .0f, 1.f);
        glClear(GL_COLOR_BUFFER_BIT);

        //텍스쳐 유닛 사용
        glActiveTexture(GL_TEXTURE0); //0번 채널
        glBindTexture(GL_TEXTURE_2D, texture1);
        glActiveTexture(GL_TEXTURE1); //1번 채널
        glBindTexture(GL_TEXTURE_2D, texture2);

        // 변환행렬
        glm::mat4 transform = glm::mat4(1.0f);
        transform = glm::rotate(transform, (float)glfwGetTime(), glm::vec3(0.0f, 0.0f, 1.0f));
        transform = glm::scale(transform, glm::vec3(0.5f, 0.5f, 0.5f));
        transform = glm::translate(transform, glm::vec3(0.5f, -0.5f, 0.0f));

        // 쉐이더 사용
        shaderProgram.useShader();
        unsigned int transformLoc = glGetUniformLocation(shaderProgram.shaderProgramID, &quot;transform&quot;);
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(transform));

        //VAO 활성화
        glBindVertexArray(VAO);
        // 그리기
        //glDrawArrays(GL_TRIANGLES, 0, 3);
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        //역회전
        transform = glm::mat4(1.0f);
        transform = glm::rotate(transform, -(float)glfwGetTime(), glm::vec3(0.0f, 0.0f, 1.0f));
        transform = glm::scale(transform, glm::vec3(0.5f, 0.5f, 0.5f));
        transform = glm::translate(transform, glm::vec3(0.5f, -0.5f, 0.0f));
        shaderProgram.useShader();
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(transform));
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        //랜덤 1사분면
        transform = glm::mat4(1.0f);
        transform = glm::translate(transform, glm::vec3(0.5f, 0.5f, 0.0f));
        transform = glm::rotate(transform, static_cast&lt;float&gt;(glfwGetTime()) * 8, glm::vec3(rand1, rand1, rand1));
        transform = glm::scale(transform, glm::vec3(0.7f, 0.7f, 0.7f));
        shaderProgram.useShader();
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(transform));
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        //랜덤 2사분면
        transform = glm::mat4(1.0f);
        transform = glm::translate(transform, glm::vec3(-0.5f, 0.5f, 0.0f));
        transform = glm::rotate(transform, static_cast&lt;float&gt;(glfwGetTime()) * 8, glm::vec3(rand2, rand2, rand2));
        transform = glm::scale(transform, glm::vec3(0.7f, 0.7f, 0.7f));
        shaderProgram.useShader();
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(transform));
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        //랜덤 3사분면
        transform = glm::mat4(1.0f);
        transform = glm::translate(transform, glm::vec3(-0.5f, -0.5f, 0.0f));
        transform = glm::rotate(transform, static_cast&lt;float&gt;(glfwGetTime()) * 8, glm::vec3(rand3, rand3, rand3));
        transform = glm::scale(transform, glm::vec3(0.7f, 0.7f, 0.7f));
        shaderProgram.useShader();
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(transform));
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        //랜덤 4사분면
        transform = glm::mat4(1.0f);
        transform = glm::translate(transform, glm::vec3(0.5f, -0.5f, 0.0f));
        transform = glm::rotate(transform, static_cast&lt;float&gt;(glfwGetTime()) * 8, glm::vec3(rand4, rand4, rand4));
        transform = glm::scale(transform, glm::vec3(0.7f, 0.7f, 0.7f));
        shaderProgram.useShader();
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(transform));
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);


        //VAO비활성화
        glBindVertexArray(0);

        //swapping buffer frame, and execute event as like input or something
        glfwSwapBuffers(window);
        glfwPollEvents();    
    }

    //optional, don't need it but keep do it until used to OpenGL
    glDeleteBuffers(1, &amp;VBO);
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

    if (glfwGetKey(window, GLFW_KEY_UP) == GLFW_PRESS)
    {
        if (lerp &gt;= 0.9f) return;
        lerp += 0.01f;
    }
    else if (glfwGetKey(window, GLFW_KEY_DOWN) == GLFW_PRESS)
    {
        if (lerp &lt;= 0.1f) return;
        lerp -= 0.01f;
    }
}
</code></pre>
<p>while문에서 약간 수정함</p>
<p>1개는 </p>
<ol>
<li>회전</li>
<li>스케일</li>
<li>이동</li>
</ol>
<p>순서로 먼저 그리고</p>
<p>5개를 추가로 그림
1개는 역회전
나머지 4개는 각 사분면에서 랜덤 회전축을 가지도록 ㅇㅇ</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/2b2cd0f1-5aa8-40f9-bfc1-bad9463a6eca/image.gif" /></p>
<p>ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ</p>