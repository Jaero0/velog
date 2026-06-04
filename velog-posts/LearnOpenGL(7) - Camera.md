<p><a href="https://velog.io/@vfx_master/LearnOpenGL6-%EC%A2%8C%ED%91%9C-%EC%8B%9C%EC%8A%A4%ED%85%9C">LearnOpenGL(6) - 좌표 시스템</a>
여기서 view공간에 대해서 설명할때 다음 포스트에서 자세하게 다룬다고 했음</p>
<p>그게 여기임ㅇㅇ</p>
<p>웰컴투 히어</p>
<h1 id="뷰-공간">뷰 공간</h1>
<p>뷰 공간은 카메라(관찰자)로부터 공간의 특정 지점을 바라본 공간을 의미함</p>
<p>즉, 카메라가 매우 중요하단걸 알 수 있음</p>
<h2 id="1-카메라-위치">1. 카메라 위치</h2>
<p>카메라 위치를 정해야함</p>
<pre><code class="language-cpp">auto cameraPos = glm::vec3(0,0,3);</code></pre>
<p>이런식으로 카메라의 위치를 정해주면 됨</p>
<p>여기서 중요한거!!!</p>
<blockquote>
<p>오른손 좌표계냐 왼손 좌표계냐에 따라 카메라의 위치가 달라짐</p>
</blockquote>
<p><strong>오른손좌표계</strong>
양의 x축으로 엄지를 뻗고, 양의 y축으로 검지를 뻗었을때,
중지는 오른손 좌표계일때, 나를 가리키고 있음
즉, 그게 양의 z축이 중지가 가리키는 방향이고, 그게 나를 가리킨다는 거임
따라서 z축에 양수를 더하면 나한테 가까워지고
음수를 더하면 나한테서 멀어짐</p>
<blockquote>
</blockquote>
<p><strong>왼손좌표계</strong>
양의 x축으로 엄지를 뻗고, 양의 y축으로 검지를 뻗었을때,
중지는 왼손 좌표계일때, 나의 반대방향 가리키고 있음
즉, 그게 양의 z축이 중지가 가리키는 방향이고, 그게 나의 반대 방향이라는거임
따라서 z축에 양수를 더하면 나한테 더 멀어지고
음수를 더하면 나한테 더 가까워짐</p>
<blockquote>
</blockquote>
<p>카메라위치를 정할때는 좌표계에 맞춰 z축을 조정해야함!</p>
<h2 id="2-카메라-방향">2. 카메라 방향</h2>
<p>카메라 방향을 정해줘야함</p>
<p>이것도 좌표계에 따라 달라짐</p>
<p>기본적으로 GL은 뷰 행렬에 사용할 z축은 양수를 원함(desire)
그리고 우리가 사용하고 있는 좌표계는 오른손 좌표계이므로
카메라의 방향은 음의 z축을 향하고 있음</p>
<p>따라서 카메라가 찍기를 원하는 타겟의 좌표가 p라고 햇을때
<code>p.z</code>와 <code>cameraPos.z</code>의 순서를 적절하게 바꿔 계산을 해서
벡터 방향이 양의 z를 향하도록 하면 됨!</p>
<pre><code class="language-cpp">auto cameraTarget = glm::vec3(0,0,0);
auto cameraDir = glm::normalize(cameraPos - cameraTarget);</code></pre>
<h2 id="3-카메라-right-방향">3. 카메라 right 방향</h2>
<p>지금의 카메라는 위아래가 뒤집힌건지 뭔지 모름</p>
<p>예를들어 카메라의 위아래가 뒤집혀잇다고 해보자</p>
<p><code>cameraPos</code>, <code>cameraDir</code>모두 값에 차이가 없음
근데 <code>y좌표 + 양수</code>를 하면 카메라가 아래로 움직임</p>
<p>왜냐면 카메라는 로컬공간에서 움직이는거라 
up방향으로 움직이고있는데
이는 월드좌표에서 down방향인거거덩</p>
<p>그걸 해결하기 위해 먼저 카메라의 right 방향을 구한다음
그걸 이용해서 up 방향을 구해야함</p>
<p>right는 쉬움</p>
<p>외적하면됨</p>
<p><code>월드의 up</code> 방향과<code>cameraDirection</code> 방향을 외적하면 
두 벡터의 수직인 벡터가 나오지?</p>
<p>이게 right방향벡터임</p>
<blockquote>
<p>왜 up과 cameraDirection임?</p>
</blockquote>
<p>외적은 순서에 따라 결과가 반대가 됨
외적은 방향이 순환되는 특성이 있음</p>
<blockquote>
</blockquote>
<img src="https://velog.velcdn.com/images/vfx_master/post/5ee09e5a-5ed2-4459-b2cd-03fb875f166c/image.png" width="30%/" />
이걸 잘 봐보자
>
- $X \times Y = Z$
- $Y \times Z = X$
- $Z \times X = Y$
>
가 성립하는걸 볼 수 있음!(모르겠다면 엄지를 시작, 검지를 연산에 놓으면 중지가 결과 방향이 됨)
>
즉 이걸 다시 정리해보면
- $right \times up = dir$
- $up \times dir = right$
- $dir \times right = up$
>
이 되는걸 볼 수 있음
>>잘 모르겠다면 right = $1,0,0$, up = $0,1,0$, dir = $0,0,1$로 외적 계산 ㄱㄱ

<p>따라서 우리에게 필요한건 월드좌표기준 up벡터와 그 up과 카메라를 향하는 방향(양의 z축)을 외적하면 카메라의 right벡터가 나옴!!!</p>
<pre><code class="language-cpp">auto worldUp = glm::vec3(0,1,0);
auto cameraRight = glm::normalize(glm::cross(worldUp, cameraDir));</code></pre>
<p>normalize를 해야하는 이유는</p>
<ol>
<li>여기서 right를 이용해 camera의 local up을 구하게 됨</li>
<li>소수점의 연산은 항상 정확하지 않아서 오차가 발생</li>
<li>더군다나 worldUp과 cameraDir은 완벽한 90도가 아님</li>
<li>오차가 발생한 벡터를 외적을 이용해 연산을 하면 오차가 커지는 경우 발생</li>
</ol>
<p>따라서 모든 방향벡터의 연산에는 normalize가 필요함</p>
<h2 id="카메라-up방향">카메라 up방향</h2>
<p>이제 카메라의 up을 구할 수 있게됨</p>
<p>위에서 설명한대로
$dir \times right = up$이 성립함</p>
<p>그러니,,,</p>
<pre><code class="language-cpp">autp cameraUp = glm::cross(cameraDir, cameraRight);</code></pre>
<p>를함</p>
<blockquote>
<p>??? 여기선 왜 normalize안함??</p>
</blockquote>
<p>일반적으로 normalize는 무거운 연산임
그리고 이미 dir과 right는 수직이 확실함
그러니 up도 완벽하게 수직이라는걸 알 수있음</p>
<blockquote>
</blockquote>
<p>그러니까 normalize를 건너뛰어도 되는거임</p>
<h1 id="glmlookat">glm::lookAt</h1>
<p>그냥 GOAT임</p>
<p>위 계산과정을 한번에 파라미터로 넘기기만하면 수행해줌</p>
<p>사실 뷰 행렬은 간단함</p>
<p>$M_{view} = \begin{bmatrix}
R_x &amp; R_y &amp; R_z &amp; 0\
U_x &amp; U_y &amp; U_z &amp; 0\
D_x &amp; D_y &amp; D_z &amp; 0\
0 &amp; 0 &amp; 0 &amp; 1
\end{bmatrix}$</p>
<p>임
$R$ : 카메라의 right
$U$ : 카메라의 up
$D$ : 카메라 Dir</p>
<p>그럼 lookAt은 어떻게 작동하냐&gt;?</p>
<p>$lookAt = \begin{bmatrix}
R_x &amp; R_y &amp; R_z &amp; 0\
U_x &amp; U_y &amp; U_z &amp; 0\
D_x &amp; D_y &amp; D_z &amp; 0\
0 &amp; 0 &amp; 0 &amp; 1
\end{bmatrix} * 
\begin{bmatrix}
0&amp;0&amp;0&amp; -P_x\
0&amp;0&amp;0&amp;-P_y\
0&amp;0&amp;0&amp;-P_z\
0 &amp; 0 &amp; 0 &amp; 1
\end{bmatrix}$</p>
<p>임</p>
<blockquote>
<p>왜 $-P$임?</p>
</blockquote>
<p>뷰 공간은 카메라 공간이라고 부름
이때 특이한 점은 카메라가 움직인다는 개념이 아님
카메라는 가만히 있고, 카메라를 기준으로 배경이 회전하고 움직인다는 느낌임</p>
<blockquote>
</blockquote>
<p>약간 기차 생각하면 됨
세상 관점에서 보면 기차가 움직이고 풍경은 가만히 있음
근데 기차에 타고있는 사람관점에서보면 풍경이 나를 지나쳐감</p>
<blockquote>
</blockquote>
<p>즉 내가 기차를 타고 5만큼 이동했다면
이거는 풍경이 기차를 기준으로 -5만큼 이동했다고 보면 되는거임</p>
<blockquote>
</blockquote>
<p>따라서 현재 <code>cameraPos</code>를 x,y,z만큼 이동시켰으니
이거를 원점으로 돌려 카메라를 기준으로 회전시키기 위해 역를 기준으로 회전시키기 위해 역원을 더해 0으로 만들어주는거임</p>
<pre><code class="language-cpp">glm::mat4 view;
view = glm::lookAt(glm::vec3(0.0f, 0.0f, 3.0f), 
             glm::vec3(0.0f, 0.0f, 0.0f), 
             glm::vec3(0.0f, 1.0f, 0.0f));</code></pre>
<ul>
<li>첫번째 : CameraPosition</li>
<li>두번째 : CameraTarget(카메라가 보는 지점)</li>
<li>세번째 : WorldUp벡터</li>
</ul>
<p>끝!</p>
<h2 id="예시">예시</h2>
<pre><code class="language-cpp">const float radius = 10.0f;
float camX = sin(glfwGetTime()) * radius;
float camZ = cos(glfwGetTime()) * radius;
glm::mat4 view;
view = glm::lookAt(glm::vec3(camX, 0.0, camZ), glm::vec3(0.0, 0.0, 0.0), glm::vec3(0.0, 1.0, 0.0));</code></pre>
<p>뷰 행렬을 회전하도록 해봤음</p>
<p>그럼 카메라가 회전하는것처럼 보이겠지??</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/ad0c1470-e16c-4f83-b9ea-c0bef4c08dab/image.gif" /></p>
<p>빙빙 돌아가는~~</p>
<h1 id="input받아서-카메라-이동">input받아서 카메라 이동</h1>
<h2 id="wasd이동">wasd이동</h2>
<p>먼저 3개의 좌표를 만들어줌</p>
<pre><code class="language-cpp">auto cameraPos = glm::vec3(0,0,3);
auto cameraFront = glm::vec3(0,0,-1); //카메라가 보는 방향 벡터
auto worldUp = glm::vec3(0,1,0);

view = glm::lookAt(cameraPos, cameraPos + cameraFront, cameraUp);</code></pre>
<p>여기서 cameraFront를 더해주는 이유는
그냥 카메라의 정면을 계속 보도록 하는거임</p>
<p><code>CameraPosition</code> - <code>원점</code> 을 하면 원점만 바라보는데,
<code>cameraPosition</code> + <code>CameraFrontDir</code>를 하면 카메라가 바라보는 방향이 나오게 되는거지</p>
<pre><code class="language-cpp">//key input callback
void process_input(GLFWwindow *window)
{
    //...    
    const float cameraSpeed = 0.05f; // adjust accordingly

    if (glfwGetKey(window, GLFW_KEY_W) == GLFW_PRESS)
        cameraPos += cameraSpeed * cameraFront;
    if (glfwGetKey(window, GLFW_KEY_S) == GLFW_PRESS)
        cameraPos -= cameraSpeed * cameraFront;
    if (glfwGetKey(window, GLFW_KEY_A) == GLFW_PRESS)
        cameraPos -= glm::normalize(glm::cross(cameraFront, worldUp)) * cameraSpeed;
    if (glfwGetKey(window, GLFW_KEY_D) == GLFW_PRESS)
        cameraPos += glm::normalize(glm::cross(cameraFront, worldUp)) * cameraSpeed;
}</code></pre>
<p>input관리하는 이벤트 콜백함수에서 
대충 이런식으로 만들어줌</p>
<p>앞으로갈때는 <code>cameraPos += cameraPos * cameraFront</code>를 해서
카메라를 음의 z축(오른손 좌표계 기준)으로 움직이고
뒤로갈때는 그 반대,</p>
<p>오른쪽으로 갈때는 위에서 구한 right를 구하는 방법을 이용해서
오른손좌표계 기준 엄지를 front방향, 검지를 up방향으로 만들면 중지가 오른쪽 방향이므로 그 방향을 빼면, 왼쪽으로 이동
더하면 오른쪽으로 이동이 됨
정규화 필수!!! </p>
<h3 id="deltatime">deltaTime</h3>
<p>사람들 컴터마다 연산처리속도가 다름
그래서 일반적인 프레임으로 처리를 해버리면 누구는 빠르고 누구는 느리게 됨</p>
<p>그래서 deltaTime을 계산해줘야함</p>
<p>고성능 PC를 A, 
개똥컴 PC를 B라고 부르도록 하겠음</p>
<p>A는 1초에 10번 프레임 호출이 됨 (10fps)
B는 1초에 1번 프레임 호출이 됨 (1fps)</p>
<p>이때 각 프레임간의 호출시간 차이를 deltaTime이라고 하면
A의 deltaTime = 0.1초
B의 deltaTime = 1초
가 됨</p>
<p>이때 <code>cameraSpeed</code>가 2일때
A의 <code>deltaTime * cameraSpeed = 0.1 * 2 = 0.2</code> (0.1초단위)
B의 <code>deltaTime * cameraSpeed = 2</code> (1초단위)
즉, 1초가 같이 흐르게 되면
A컴퓨터와 B컴퓨터의 이동거리는 <code>2</code>로 동일한 거리만큼 이동되게 됨</p>
<pre><code class="language-cpp">float deltaTime = 0.0f;
float lastFrame = 0.0f;</code></pre>
<p>이런 코드를 최상단에 선언해줌</p>
<p>그리고 매 프레임 시간을 계산해야됨
이거는 <code>glfwGetTime()</code>을 통해 아주 쉽게 계산가능함</p>
<pre><code class="language-cpp">float currentFrame = glfwGetTime();
deltaTime = currentFrame - lastFrame;
lastFrame = currentFrame; </code></pre>
<p>그리고 이렇게 계산된 deltaTime을 cameraSpeed에 적용시켜주면...</p>
<pre><code class="language-cpp">void processInput(GLFWwindow *window)
{
    float cameraSpeed = 5.0f * deltaTime;
    //...
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/7cb4c8f3-a956-4a07-ad1c-ed3205cd26d2/image.gif" /></p>
<p>ㅋㅋㅋ</p>
<h2 id="마우스-이동">마우스 이동</h2>
<p>오일러 각을 알아야함</p>
<p><a href="https://velog.io/@vfx_master/LearnOpenGL5-glm">LearnOpenGL(5) - glm</a> 여기서 오일러 각에 대해 간단하게 알아보고 오자!</p>
<p>현재 카메라시스템에서는 z축 회전이 필요없음!
배그같이 기울이기 되는거면 필요하겠지만,,,</p>
<p>y축의 회전은 yaw, x축 회전은 pitch임</p>
<p>우리는 마우스 이동을 통해 <strong>카메라가 바라보는 방향</strong>을 바꾸고자 하는거임</p>
<h3 id="yaw회전">yaw회전</h3>
<p>yaw는 y축으로의 회전임
따라서 y좌표는 변하지 않음</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/aace6130-f29b-431e-8546-86c01f0526fb/image.png" /></p>
<p>이걸보면 y좌표를 제외하고 좌표를 평면으로 본거임
이때 빗변과 밑변의 각을 yaw라고 할때,
yaw가 증가하거나 감소할때
$\cos x = x / h(빗변) = x / 1 = \cos \text{yaw}$가 됨
$sin z = z / h(빗변) = z / 1 = \sin \text{yaw}$가 됨</p>
<p>따라서 카메라가 보는 방향을 <code>camDir</code>이라고 할때</p>
<pre><code class="language-cpp">glm::vec3 camDir;
camDir.x = cos(glm::radians(yaw));
camDir.z = sin(glm::radians(yaw));</code></pre>
<p>가 됨</p>
<h3 id="pitch-회전">pitch 회전</h3>
<p>위의 yaw회전 결과에 pitch회전 결과만 붙이면 그게 바로 카메라가 보는 방향임</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/068a07d5-9e61-4984-8407-039ffb3595ca/image.png" /></p>
<p>위의 yaw회전을 할때, yaw는 x/z축과 관계가 있었음
그 x/z 평면에서 pitch를 구하는것이므로 $x/z$가 된거임</p>
<p>이때 빗변과 밑변(x/z평면)의 각이 pitch라고 할때
$\sin y = y / h = y / 1 = \sin \text{pitch}$
가됨</p>
<pre><code class="language-cpp">camDir.y = sin(glm::radians(pitch));</code></pre>
<p>가 됨</p>
<h3 id="pitch영향은-xz-평면에게도-있음">pitch영향은 x/z 평면에게도 있음</h3>
<p>우리 눈이 카메라라고 해보자</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6eec56b0-495d-4821-9881-ebba8c6343b4/image.png" /></p>
<p>이 그림을 봐봐</p>
<p>카메라가 정면을 보면 위에서 살펴봤듯이 $\sin \text{pitch} = 0$임
이때 $\cos \text{pitch} = 1$로, 온전히 x/z평면에 귀속됨</p>
<p>근데 만약 카메라가 정확히 90도, 정수리 위를 본다면?
$\sin \text{pitch} = 1$이 됨
이때 $\cos \text{pitch}$는?
우리 눈이 앞으로 튀어나오지 않는 이상 
정확히 $\cos \text{pitch} = 0$이 되고, x/z는 0인 상태에서 위를 바라보는거라고 해야됨</p>
<p>따라서 $\cos \text{pitch}$는 x/z평면에 영향을 끼침</p>
<p>$\cos \text{pitch}$가 1이면 온전히 x/z평면에 귀속되고
이 값이 변화함에따라 x/z평면의 방향과 블렌딩이 된다고 봐야하는거지 ㅇㅇ</p>
<p>따라서 전체 코드는</p>
<pre><code class="language-cpp">glm::vec3 camDir;
camDir.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
camDir.y = sin(glm::radians(pitch));
camDir.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));</code></pre>
<p>가 됨을 알 수 있음!!</p>
<blockquote>
<p>여기에 z축 회전이 추가되어야 한다면
x/y평면에 대하여 z축의 $\sin$을 구한다음 
마찬가지로 영향을 끼치는 걸 판단해서 구해주면 되겠지</p>
</blockquote>
<h3 id="input처리">input처리</h3>
<pre><code class="language-cpp">glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED);</code></pre>
<p>먼저 이런 코드를 작성해서
커서를 창에 박아놓음
창밖으로 나가지 않고, 커서는 안보이고 ㅇㅇ</p>
<p>그리고 마우스는 마우스 이동 이벤트를 받아서 처리해야하므로</p>
<pre><code class="language-cpp">void mouse_callback(GLFWwindow* window, double xpos, double ypos);</code></pre>
<p>이런 콜백을 만들어서 사용할거니 전방선언 후</p>
<p>그리고</p>
<pre><code class="language-cpp">bool firstMouse = true;
float yaw   = -90.0f;    
float pitch =  0.0f;
float lastX =  800.0f / 2.0;
float lastY =  600.0 / 2.0;
float fov   =  45.0f;</code></pre>
<p>이런 코드를 전역으로 선언해준다</p>
<blockquote>
<p>yaw가 -90인 이유</p>
</blockquote>
<ol>
<li>yaw = 0일때
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/c0cd8299-1787-4ce0-a616-3c9c44454373/image.png" />
이걸 보면 yaw가 0이면 완전 오른쪽을 바라보게 됨
즉, 정면이 아니라 오른쪽을 본상태로 시작한다는거지...</li>
<li>yaw = 90일때
위사진을 보면 yaw가 90이면 완전 양의 z축을 바라보게됨
근데 우리는 오른손 좌표계를 사용중임
즉, 90을 사용하면 카메라에서 양의 z축인 뒷편을 바라보게 된다는거임<blockquote>
</blockquote>
이런이유로 -90도를 사용해서 카메라에서 앞편인 음의 z축을 바라보게 해야함</li>
</ol>
<p>그리고 마우스 이동 콜백은 한번만 수행하면 되므로
main 최상단에 박아버려~</p>
<pre><code class="language-cpp">//마우스 이동 인풋
glfwSetCursorPosCallback(window, mouse_callback); 
</code></pre>
<p>이제 콜백 메서드를 작성할 차례임</p>
<pre><code class="language-cpp">void mouse_callback(GLFWwindow* window, double xpos, double ypos)
{
    if (firstMouse)
    {
        lastX = xpos;
        lastY = ypos;
        firstMouse = false;
    }

    float xoffset = xpos - lastX;
    float yoffset = lastY - ypos; 
    lastX = xpos;
    lastY = ypos;

    float sensitivity = 0.1f;
    xoffset *= sensitivity;
    yoffset *= sensitivity;

    yaw   += xoffset;
    pitch += yoffset;

    pitch = glm::clamp(pitch, -89.0f, 89.0f);

    glm::vec3 direction;
    direction.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
    direction.y = sin(glm::radians(pitch));
    direction.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));
    cameraFront = glm::normalize(direction);
}  </code></pre>
<p>천천히 살펴보자</p>
<h4 id="마우스-초기화">마우스 초기화</h4>
<pre><code class="language-cpp">if (firstMouse)
{
    lastX = xpos;
    lastY = ypos;
    firstMouse = false;
}</code></pre>
<p>처음 프로그램이 실행되면 아무 값도 지정되어있지 않으므로
lastX와 lastY좌표를 현재 마우스 좌표로 넣어버림</p>
<h4 id="마우스-좌표-offset-구하기">마우스 좌표 offset 구하기</h4>
<pre><code class="language-cpp">float xoffset = xpos - lastX;
float yoffset = lastY - ypos; 
lastX = xpos;
lastY = ypos;

float sensitivity = 0.1f;
xoffset *= sensitivity;
yoffset *= sensitivity;

yaw   += xoffset;
pitch += yoffset;</code></pre>
<ol>
<li>현재 마우스 x,y좌표와
마지막 마우스 x,y좌표의 차를 구함</li>
<li>마지막 마우스 x,y좌표를 현재 마우스 x,y좌표로 바꿈</li>
<li>마우스 감도에 따라 좌표 차를 보정함</li>
<li>화면의 가로는 x이고 이는 yaw에 영향을 주므로 yaw에 마우스 x좌표 차를 더함
화면의 세로는 y이고 이는 pitch에 영향을 주므로, pitch에 마우스 y좌표 차를 더함</li>
</ol>
<h4 id="pitch-clamping">pitch clamping</h4>
<pre><code class="language-cpp">pitch = glm::clamp(pitch, -89.0f, 89.0f);</code></pre>
<p>clamp임</p>
<p>우리가 right방향을 구할때
<code>worldUp = (0,1,0)</code>과 외적했던거 기억남?</p>
<p>방향이 아예 같거나(0도 차이) 완전히 반대방향(180도차이)같은 두 벡터를 외적하면 
외적 결과는 영 벡터가 됨
그럼 right벡터는 영 벡터인가?
말이 안되지 ㅇㅇㅇ</p>
<p>그러므로 이 worldUp벡터와 카메라의 방향 벡터가 평행이 되지 않는 조건을 만들기위해
-89~89도 사이로 위아래에 영향을 주는 pitch를 clamping하는거임</p>
<h4 id="카메라-방향">카메라 방향</h4>
<pre><code class="language-cpp">glm::vec3 direction;
direction.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
direction.y = sin(glm::radians(pitch));
direction.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));
cameraFront = glm::normalize(direction);</code></pre>
<p>위에서 살펴본 공식을 이용해 카메라가 보는 방향을 구하면 됨</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/83fd126b-5dcb-4429-9407-3e1a14011717/image.gif" />
윈도우 가로세로의 종횡비에 따라
가로 감도, 세로 감도를 직접 지정해주면 더 좋긴해~</p>
<h2 id="확대-축소">확대 축소</h2>
<p>그냥 이건 졸라 간단해!</p>
<p>확대하면 fovy가 감소해서 더 적은 부분만 렌더링하면 되는거고
축소하면 fovy가 증가해서 더 많은 부분을 렌더링하도록 하면 되는거임!</p>
<pre><code class="language-cpp">void scroll_callback(GLFWwindow* window, double xoffset, double yoffset)
{
    fov -= (float)yoffset;

    fov = glm::clamp(fov, 1.0f, 45.0f);
}</code></pre>
<p>대충 이런 콜백 메서드 만들어주고</p>
<p>전방선언하고</p>
<pre><code class="language-cpp">glfwSetScrollCallback(window, scroll_callback);</code></pre>
<p>넣어주면 끄읕~~</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/811233e1-7d66-4739-9c06-7f661ebc30b4/image.gif" /></p>
<h1 id="카메라-클래스-만들기">카메라 클래스 만들기</h1>
<pre><code class="language-cpp">#ifndef CAMERA_H
#define CAMERA_H

#include &lt;glad/glad.h&gt;
#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;


enum Camera_Movement 
{
    FORWARD,
    BACKWARD,
    LEFT,
    RIGHT
};

const float YAW         = -90.0f;
const float PITCH       =  0.0f;
const float SPEED       =  2.5f;
const float SENSITIVITY =  0.1f;
const float ZOOM        =  45.0f;


class Camera
{
public:
    glm::vec3 Position;
    glm::vec3 Front;
    glm::vec3 Up;
    glm::vec3 Right;
    glm::vec3 WorldUp;

    float Yaw;
    float Pitch;
    float MovementSpeed;
    float MouseSensitivity;
    float Zoom;

    Camera(glm::vec3 position = glm::vec3(0.0f, 0.0f, 0.0f), glm::vec3 up = glm::vec3(0.0f, 1.0f, 0.0f), float yaw = YAW, float pitch = PITCH) : Front(glm::vec3(0.0f, 0.0f, -1.0f)), MovementSpeed(SPEED), MouseSensitivity(SENSITIVITY), Zoom(ZOOM)
    {
        Position = position;
        WorldUp = up;
        Yaw = yaw;
        Pitch = pitch;
        updateCameraVectors();
    }

    Camera(float posX, float posY, float posZ, float upX, float upY, float upZ, float yaw, float pitch) : Front(glm::vec3(0.0f, 0.0f, -1.0f)), MovementSpeed(SPEED), MouseSensitivity(SENSITIVITY), Zoom(ZOOM)
    {
        Position = glm::vec3(posX, posY, posZ);
        WorldUp = glm::vec3(upX, upY, upZ);
        Yaw = yaw;
        Pitch = pitch;
        updateCameraVectors();
    }

    glm::mat4 GetViewMatrix()
    {
        return glm::lookAt(Position, Position + Front, Up);
    }


    void ProcessKeyboard(Camera_Movement direction, float deltaTime)
    {
        float velocity = MovementSpeed * deltaTime;
        if (direction == FORWARD)
            Position += Front * velocity;
        if (direction == BACKWARD)
            Position -= Front * velocity;
        if (direction == LEFT)
            Position -= Right * velocity;
        if (direction == RIGHT)
            Position += Right * velocity;
    }

    //때때로 pitch를 clamp하지 말아야할 경우도 생김
    //예를들어 비행기 조작 ㅇㅇ
    그럴땐 다른 방법을 써야지~
    void ProcessMouseMovement(float xoffset, float yoffset, GLboolean constrainPitch = true) 
    {
        xoffset *= MouseSensitivity;
        yoffset *= MouseSensitivity;

        Yaw   += xoffset;
        Pitch += yoffset;

        if (constrainPitch)
        {
            Pitch = glm::clamp(Pitch, -89.0f, 89.0f);
        }

        updateCameraVectors();
    }

    void ProcessMouseScroll(float yoffset)
    {
        Zoom -= (float)yoffset;

        Zoom = glm::clamp(Zoom, 1.0f, 45.0f);
    }

private:

    void updateCameraVectors()
    {
        glm::vec3 front;
        front.x = cos(glm::radians(Yaw)) * cos(glm::radians(Pitch));
        front.y = sin(glm::radians(Pitch));
        front.z = sin(glm::radians(Yaw)) * cos(glm::radians(Pitch));

        Front = glm::normalize(front);
        Right = glm::normalize(glm::cross(Front, WorldUp));
        Up    = glm::normalize(glm::cross(Right, Front));
    }
};
#endif</code></pre>
<p>이런식으로 카메라에 필요한 코드를 옮김</p>
<h1 id="달라진-전체코드">달라진 전체코드</h1>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;
#include &lt;random&gt;
#include &lt;glm/glm.hpp&gt;
#include &lt;glm/gtc/matrix_transform.hpp&gt;
#include &lt;glm/gtc/type_ptr.hpp&gt;

#include &quot;Camera.h&quot;
#include &quot;Shader.h&quot;
#include &quot;stb_image.h&quot;

//전방선언
void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void process_input(GLFWwindow *window);
void mouse_callback(GLFWwindow* window, double xpos, double ypos);
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset);

//윈도우 사이즈
constexpr int windowWidth = 1280;
constexpr int windowHeight = 720;

//deltaTime용
float deltaTime = 0.0f;
float lastFrame = 0.0f;

//카메라 클래스
Camera camera(glm::vec3(0.0f, 0.0f, 3.0f));
float lastX =  800.0f / 2.0;
float lastY =  600.0 / 2.0;
bool firstMouse = true;

int main()
{


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
    //마우스 이동 인풋
    glfwSetCursorPosCallback(window, mouse_callback); 
    //마우스 스크롤 인풋
    glfwSetScrollCallback(window, scroll_callback);
    //마우스 창에 가두기
    glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED);
    //z,depth 버퍼
    glEnable(GL_DEPTH_TEST);

    // 쉐이더 생성
    Shader shaderProgram(
        &quot;D:/01_Develope/CPP/learn_opengl/learn_opengl/VertexShaderSource.vsh&quot;, 
        &quot;D:/01_Develope/CPP/learn_opengl/learn_opengl/FragmentShaderSource.fsh&quot;);


    //--------------------------------------
    //버텍스 버퍼, 버텍스 어레이, 버텍스 속성 링킹
    //--------------------------------------
    float vertices[] = 
    {
        //포지션               텍스처 좌표
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
    };

    // VAO
    uint32_t VAO;
    glGenVertexArrays(1, &amp;VAO);
    glBindVertexArray(VAO);

    // VBO
    uint32_t VBO;
    glGenBuffers(1, &amp;VBO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    // 버텍스 속성 링킹
    //position
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 5, (void*)0);
    glEnableVertexAttribArray(0);
    //텍스쳐 버텍스
    glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, sizeof(float) * 5, (void*)(sizeof(float) * 3));
    glEnableVertexAttribArray(2);

    // GL에게 버텍스 매핑은 끝이므로, 더이상 바인딩 안한다고 명시적으로 알림
    glBindVertexArray(0);

    //-----------
    //텍스쳐 만들기
    //-----------
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
        //입력 인풋
        process_input(window);

        glClearColor(.0f, .0f, .0f, 1.f);
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

        //deltaTime 초기화
        float currentFrame = glfwGetTime();
        deltaTime = currentFrame - lastFrame;
        lastFrame = currentFrame;

        //텍스쳐 유닛 사용
        glActiveTexture(GL_TEXTURE0); //0번 채널
        glBindTexture(GL_TEXTURE_2D, texture1);
        glActiveTexture(GL_TEXTURE1); //1번 채널
        glBindTexture(GL_TEXTURE_2D, texture2);

        shaderProgram.useShader();

        // 모델, 뷰, 투영 행렬
        glm::mat4 view;
        view = camera.GetViewMatrix();

        glm::mat4 projection = glm::mat4(1.0f);
        projection = glm::perspective(glm::radians(camera.Zoom), (float)windowWidth/windowHeight, 0.1f, 100.0f);

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
        }



        //VAO 활성화
        //glBindVertexArray(VAO);
        // 그리기
        //glDrawArrays(GL_TRIANGLES, 0, 36);
        //glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);


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

    if (glfwGetKey(window, GLFW_KEY_W) == GLFW_PRESS)
        camera.ProcessKeyboard(FORWARD, deltaTime);
    if (glfwGetKey(window, GLFW_KEY_S) == GLFW_PRESS)
        camera.ProcessKeyboard(BACKWARD, deltaTime);
    if (glfwGetKey(window, GLFW_KEY_A) == GLFW_PRESS)
        camera.ProcessKeyboard(LEFT, deltaTime);
    if (glfwGetKey(window, GLFW_KEY_D) == GLFW_PRESS)
        camera.ProcessKeyboard(RIGHT, deltaTime);
}

//마우스 입력 콜백
void mouse_callback(GLFWwindow* window, double xpos, double ypos)
{
    if (firstMouse)
    {
        lastX = xpos;
        lastY = ypos;
        firstMouse = false;
    }

    float xoffset = xpos - lastX;
    float yoffset = lastY - ypos; 
    lastX = xpos;
    lastY = ypos;

    camera.ProcessMouseMovement(xoffset, yoffset, true);
}  

//스크롤 입력 콜백
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset)
{
    camera.ProcessMouseScroll(yoffset);
}</code></pre>
<p>끝!</p>