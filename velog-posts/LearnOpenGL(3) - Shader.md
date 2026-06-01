<h1 id="쉐이더-입력-채널">쉐이더 입력 채널</h1>
<pre><code class="language-cpp">const char *vertexShaderSource = &quot;#version 330 core\n&quot;
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
    &quot;}\0&quot;;</code></pre>
<p>지난번에 이런 glsl을 만들었음</p>
<p>형식은 아래와 같음</p>
<pre><code class="language-cpp">#version version_number
in type in_variable_name;
in type in_variable_name;

out type out_variable_name;

uniform type uniform_name;

void main()
{
  // process input(s) and do some weird graphics stuff
  ...
  // output processed stuff to output variable
  out_variable_name = weird_stuff_we_processed;
}</code></pre>
<p>여기서 in은 입력 변수고 out은 출력 변수임</p>
<blockquote>
<p>입력변수를 버텍스 속성이라고도 함</p>
</blockquote>
<p>그래픽카드에서는 최대로 in을 받을 수 있는 변수 갯수가 정해져 잇음</p>
<pre><code class="language-cpp">int nrAttributes;
glGetIntegerv(GL_MAX_VERTEX_ATTRIBS, &amp;nrAttributes);
std::cout &lt;&lt; &quot;Maximum nr of vertex attributes supported: &quot; &lt;&lt; nrAttributes &lt;&lt; std::endl;</code></pre>
<p>이런 코드를 실행해보면,
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/0e0162f7-5547-464e-b02a-56e1c8a80b17/image.png" /></p>
<p>이런 로그가 출력됨</p>
<p>즉, 내 그래픽카드는 최대 16개의 버텍스 속성을 동시에 처리할 수 있음
그리고 각 버텍스 속성마다 최대 성분은 4성분으로 <code>vec4, mat4</code>와 같은 클래스를 의미함</p>
<blockquote>
<p>주의 : vec4가 아니라 vec2만 썼다고 총 32개의 버텍스 속성을 쓸 수 있는게 아님!!
최대 개수가 16개인거임!</p>
</blockquote>
<h1 id="glsl">GLSL</h1>
<h2 id="type">type</h2>
<p>기본적으로 GLSL에서는 cpp과 같이
<code>int</code>, <code>float</code>, <code>double</code>, <code>bool</code>, <code>uint</code>이런 타입을 사용할 수 있고
추가적으로 <code>vecor</code>, <code>matrix</code>타입이 있음</p>
<p><code>n</code> : 2,3,4의 숫자</p>
<ul>
<li>vecn: 부동소수점 벡터</li>
<li>bvecn: 불리언 벡터</li>
<li>ivecn: 정수형 벡터</li>
<li>uvecn: 부호없는 정수형 벡터</li>
<li>dvecn: double형 벡터</li>
</ul>
<h2 id="벡터-swizzling">벡터 swizzling</h2>
<p>걍 편의성임</p>
<pre><code class="language-cpp">vec2 someVec;
vec4 differentVec = someVec.xyxx;
vec3 anotherVec = differentVec.zyw;
vec4 otherVec = someVec.xxxx + anotherVec.yxzy;</code></pre>
<p>이런식으로 각 vector의 요소에 맞춰서 다른 벡터를 만들수도 있고 막 그럼</p>
<pre><code class="language-cpp">vec2 vect = vec2(0.5, 0.7);
vec4 result = vec4(vect, 0.0, 0.0);
vec4 otherResult = vec4(result.xyz, 1.0);</code></pre>
<p>이렇게 ㅇㅇ</p>
<h2 id="쉐이더간-값-공유">쉐이더간 값 공유</h2>
<ul>
<li><code>vertex</code> shader<pre><code class="language-cpp">#version 330 core
layout (location = 0) in vec3 aPos;
</code></pre>
</li>
</ul>
<p>out vec4 vertexColor;</p>
<p>void main()
{
    gl_Position = vec4(aPos, 1.0);
    vertexColor = vec4(0.5, 0.0, 0.0, 1.0);
}</p>
<pre><code>
- `fragment` shader
```cpp
#version 330 core
out vec4 FragColor;

in vec4 vertexColor; // in과 out의 같은 이름 

void main()
{
    FragColor = vertexColor;
} </code></pre><p>지금 보면 vertex shader의 <code>out</code>인 <code>vertexColor</code> vec4변수가 있음
그리고 fragment shader에서 <code>in</code>으로 vec4변수인 <code>vertexColor</code>를 입력받음</p>
<p>즉, 두 변수간의 이름이 같으면 gl이 자동적으로 연결해준다는거임ㄷㄷ</p>
<h2 id="uniform">uniform</h2>
<p>glsl에서는 uniform이라는 변수를 만들수 있음</p>
<p>이건 cpu, gpu모두 공용으로 사용할 수 있는 변수를 만드는거임
그리고 각 쉐이더 또한 이 변수를 같이 사용할 수 있음</p>
<p>사용법은 간단함</p>
<pre><code class="language-cpp">uniform vec4 smth;</code></pre>
<p>이런식으로 쓰면 되는거임</p>
<blockquote>
<p>주의 : uniform선언해두고 안쓰면 자동적을 컴파일 타임에 걸러냄...</p>
</blockquote>
<h3 id="uniform-예시">uniform 예시</h3>
<pre><code class="language-cpp">const char *fragmentShaderSource = &quot;#version 330 core\n&quot;
    &quot;out vec4 FragColor;\n&quot;
    &quot;uniform vec4 ourColor;&quot;

    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   FragColor = ourColor;\n&quot;
    &quot;}\0&quot;;</code></pre>
<p>이런 fragment shader에 uniform을 추가함</p>
<pre><code class="language-cpp"> // uniform example
        float time = glfwGetTime();
        float color = sin(time) / 2.0f + 0.5f;
        int vertexColorLocation = glGetUniformLocation(shaderProgram, &quot;ourColor&quot;);
        std::random_device rd;
        std::mt19937 gen(rd());
        std::uniform_real_distribution&lt;float&gt; dist(0.0, 1.0);

        glUseProgram(shaderProgram);
        glUniform4f(vertexColorLocation, color * dist(gen), color * dist(gen), color * dist(gen), 1.0f); //0.0~1.0사이의 랜덤 값

        // 10. delete linked shader

        glBindVertexArray(VAO);</code></pre>
<p>그리고 while문에서 <code>UseProgram</code>쪽을 수정해줌</p>
<ol>
<li><code>glfwGetTime</code>으로 현재시간을 가져온 후, 0.0~1.0사이의 값으로 변환</li>
<li><code>glGetUniformLocation</code>으로 특정 프로그램에서 특정 uniform 변수를 문자열로 찾음
만약 return 값이 -1이라면, 문자열에 해당되는 uniform을 해당 shader program에서 찾지 못한거임</li>
<li>랜덤함수 만들음</li>
<li>use program<ul>
<li>uniform 변수를 찾는데 <code>glUseProgram</code>을 할 필요는 없지만, 사용하기 위해선 <code>glUseProgram</code>을 실행해햐함</li>
</ul>
</li>
<li><code>glUniform4f</code>함수를 이용해서 vec4(부동소수점 vec4)타입인 uniform변수를 업데이트함<ul>
<li><a href="https://registry.khronos.org/OpenGL-Refpages/gl4/html/glUniform.xhtml">glUniform - OpenGL Reference</a>
여기를 보면 uniform업데이트 패밀리 함수를 볼 수 있음</li>
</ul>
</li>
</ol>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/f0b00a1f-57db-40aa-b350-1e258b6473c4/image.gif" /></p>
<h3 id="전체코드">전체코드</h3>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;
#include &lt;random&gt;

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
    &quot;uniform vec4 ourColor;&quot;
    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   FragColor = ourColor;\n&quot;
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

        // uniform example
        float time = glfwGetTime();
        float color = sin(time) / 2.0f + 0.5f;
        int vertexColorLocation = glGetUniformLocation(shaderProgram, &quot;ourColor&quot;);
        std::random_device rd;
        std::mt19937 gen(rd());
        std::uniform_real_distribution&lt;float&gt; dist(0.0, 1.0);

        glUseProgram(shaderProgram);
        glUniform4f(vertexColorLocation, color * dist(gen), color * dist(gen), color * dist(gen), 1.0f);

        // 10. delete linked shader
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

    int nrAttributes;
    glGetIntegerv(GL_MAX_VERTEX_ATTRIBS, &amp;nrAttributes);
    std::cout &lt;&lt; &quot;Maximum nr of vertex attributes supported: &quot; &lt;&lt; nrAttributes &lt;&lt; std::endl;

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
<h2 id="더-많은-속성glvertexattribpointer-offset설정">더 많은 속성(glVertexAttribPointer offset설정)</h2>
<pre><code class="language-cpp">float vertices[] = 
{
    //positions         //colors
     0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f, // 우상단
    -0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f, // 우하단
     0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f, // 좌하단
};</code></pre>
<p>먼저 vertices를 수정좀 해줌
삼각형만 출력할그그든ㅇㅇ</p>
<p>우리의 목적은 vertex마다 다른 색상을 가지도록 하는것임
따라서 우리는 vertex shader에서 설정을 해줘야함</p>
<pre><code class="language-cpp">const char *vertexShaderSource = 
    &quot;#version 330 core\n&quot;
    &quot;layout (location = 0) in vec3 aPos;\n&quot;   //vertex position attributes
    &quot;layout (location = 1) in vec3 aColor;\n&quot; //vertex color attributes

    &quot;out vec3 ourColor;\n&quot; //out in connecting

    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   gl_Position = vec4(aPos, 1.0);\n&quot;
    &quot;   ourColor = aColor;\n&quot;
    &quot;}\0&quot;;

const char *fragmentShaderSource = 
    &quot;#version 330 core\n&quot;
    &quot;out vec4 FragColor;\n&quot;
    &quot;in vec3 ourColor;\n&quot; //out in connecting

    &quot;void main()\n&quot;
    &quot;{\n&quot;
    &quot;   FragColor = ourColor;\n&quot;
    &quot;}\0&quot;;</code></pre>
<p>shader는 이런식으로 수정해줌
이번에는 vertex에 각각 색상을 지정해줌
그리고 그걸 out을 통해 fragment로 넘기고
fragment에서는 in을 통해 받음</p>
<p>그리고 지금 우리의 <code>vertices</code>를 살펴보면</p>
<p><code>[position(f3)-color(f3)] &lt;-&gt; [position(f3)-color(f3)] &lt;-&gt;....</code>
이런식으로 되어있음(f3는 float 3개라는 뜻)</p>
<p>따라서 <code>glVertexAttributePointer</code>에서 정점 매핑을 수정해줘야함</p>
<ul>
<li>vertex position : 첫 인덱스부터 3개 이후, 6개씩 offset</li>
<li>vertex color : 3번 인덱스부터 3개 이후, 6개씩 offset</li>
</ul>
<pre><code class="language-cpp">// 9-2. vertex attributes linking
//position
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 6, (void*)0);
glEnableVertexAttribArray(0);
//color
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 6, (void*)(sizeof(float) * 3));
glEnableVertexAttribArray(1);</code></pre>
<p>location에 맞춰서 0번 1번 통로 잘 설정해주고
6개씩 offset &amp; 시작 인덱스 설정!</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/1068b586-4128-4021-9cfc-383472d5ca60/image.png" /></p>
<p>추가적으로 자동 선형 보간이 되는것까지 확인!</p>
<blockquote>
<p>선형보간이 어떻게 작동하냐고??</p>
</blockquote>
<p><a href="https://velog.io/@vfx_master/%EB%82%98%EB%A7%8C%EC%9D%98-tiny-renderer-%EB%A7%8C%EB%93%A4%EA%B8%B0-3-">흑빡 - Barycentric Coordinates</a> 이걸 읽어보셈!</p>
<h1 id="쉐이더-클래스-분리">쉐이더 클래스 분리</h1>
<pre><code class="language-cpp">#ifndef SHADER_H
#define SHADER_H

#include &lt;glad/glad.h&gt;

#include &lt;fstream&gt;
#include &lt;iostream&gt;
#include &lt;sstream&gt;
#include &lt;stdint.h&gt;
#include &lt;string&gt;

class Shader
{
public:
    uint32_t shaderProgramID;

    Shader(const char* vertexPath, const char* fragmentPath)
    {
        // 1. 쉐이더 코드와 쉐이더 파일 입력스트림 생성
        std::string vertexCode;
        std::string fragmentCode;
        std::ifstream vShaderFile;
        std::ifstream fShaderFile;

        //파일 읽을수있는지 확실하게 하기
        vShaderFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
        fShaderFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);

        // 2. 쉐이더 파일 읽기
        try
        {
            //각 path에 존재하는 파일 열기
            vShaderFile.open(vertexPath);
            fShaderFile.open(fragmentPath);

            //열은 파일을 stringstream으로 읽기
            std::stringstream vShaderStream;
            std::stringstream fShaderStream;
            vShaderStream &lt;&lt; vShaderFile.rdbuf();
            fShaderStream &lt;&lt; fShaderFile.rdbuf();

            //읽은 후 파일 닫기
            vShaderFile.close();
            fShaderFile.close();

            //읽은 stringstream을 string으로 변환
            vertexCode = vShaderStream.str();
            fragmentCode = fShaderStream.str();
        }
        catch (std::ifstream::failure e)
        {
            std::cout &lt;&lt; &quot;ERROR::SHDAER::FILE_NOT_SUCCESSFULLY_READ&quot; &lt;&lt; std::endl;
        }

        //쉐이더 코드를 사용하기 위해 const char* 타입으로 변환
        const char* vShaderCode = vertexCode.c_str();
        const char* fShaderCode = fragmentCode.c_str();

        //쉐이더 프로그램 만들기
        // 1. Vertex shader
        uint32_t v = glCreateShader(GL_VERTEX_SHADER);
        glShaderSource(v, 1, &amp;vShaderCode, NULL);
        glCompileShader(v);

        // 2. check vertex shader successfully compiled
        int success;
        char log[512];
        glGetShaderiv(v, GL_COMPILE_STATUS, &amp;success);
        if (!success)
        {
            glGetShaderInfoLog(v, 512, NULL, log);
            std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
        }

        // 3. fragment shader
        uint32_t f = glCreateShader(GL_FRAGMENT_SHADER);
        glShaderSource(f, 1, &amp;fShaderCode, NULL);
        glCompileShader(f);

        // 4. check fragment shader successfully compiled
        glGetShaderiv(f, GL_COMPILE_STATUS, &amp;success);
        if (!success)
        {
            glGetShaderInfoLog(f, 512, NULL, log);
            std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
        }

        // 5. shader program
        shaderProgramID = glCreateProgram();
        glAttachShader(shaderProgramID, v);
        glAttachShader(shaderProgramID, f);
        glLinkProgram(shaderProgramID);

        // 6. check shader program successfully compiled
        glGetProgramiv(shaderProgramID, GL_LINK_STATUS, &amp;success);
        if (!success)
        {
            glGetProgramInfoLog(shaderProgramID, 512, nullptr, log);
            std::cout &lt;&lt; &quot;ERROR: &quot; &lt;&lt; log &lt;&lt; std::endl;
        }

        // 7. delete already linked shader
        glDeleteShader(v);
        glDeleteShader(f);
    }

    ~Shader()
    {
        glDeleteProgram(shaderProgramID);
    }

    void useShader()
    {
        glUseProgram(shaderProgramID);
    }

    void setBool(const std::string &amp;name, bool value) const
    {
        glUniform1i(glGetUniformLocation(shaderProgramID, name.c_str()), int(value));
    }
    void setInt(const std::string &amp;name, int value) const
    {
        glUniform1i(glGetUniformLocation(shaderProgramID, name.c_str()), value);
    }
    void setFloat(const std::string &amp;name, float value) const
    {
        glUniform1f(glGetUniformLocation(shaderProgramID, name.c_str()), value);
    }
};

#endif</code></pre>
<p>핵심은 생성자부분임
주석만 잘 읽으면 문제가 없을거니,
주석 꼼꼼히 읽고 코드가 그렇게 어렵지도 않아서,,,
그냥 잘 해석해보길!</p>
<p>이제 본인이 원하는대로 vsh, fsh와 같은 확장자로 만들어서</p>
<p>쉐이더 생성자에 담아주기만 하면 끝임!</p>
<pre><code class="language-cpp">Shader shaderProgram(
    &quot;somePath/VertexShaderSource.vsh&quot;, 
    &quot;somePath/FragmentShaderSource.fsh&quot;);

//...
while()
{
    //...
    shaderProgram.useShader();
}
</code></pre>
<p>이럼 끝임!</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/678868fa-853f-42f5-9fdc-eb38e6e42161/image.png" /></p>
<p>잘 출력댐!</p>