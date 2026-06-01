<h1 id="텍스쳐-좌표">텍스쳐 좌표</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/7a5167a6-4f4a-48fe-acf8-cd8993bb274d/image.png" /></p>
<p>이런 삼각형에 벽돌 텍스처를 입힌다고 가정해보자</p>
<p>텍스처 좌표는 도형의 절대좌표느낌으로 접근해야함</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/246c2436-77dc-4f35-96d4-5c17e25ba5cf/image.png" /></p>
<p>이런 너낌ㅇㅇ</p>
<p>즉, 도형의 좌표에서 min X, min Y 가 0,0이 되고
max X, min Y가 1,0이 됨</p>
<p>이또한 NDC, Normalized Device Coordinates라고 볼 수 있는거지 ㅇㅇ</p>
<h1 id="텍스쳐-매핑">텍스쳐 매핑</h1>
<p>텍스쳐 매핑을 위해선 gl에게
도형의 각 좌표가 텍스쳐의 어느 좌표에 해당되는지를 알려줘야함</p>
<p>위의 사진에서 봤듯이
좌하단 : <code>(0,0)</code>
중앙상단 : <code>(0.5,1)</code>
우하단 : <code>(1,0)</code> 이 삼각형 세 꼭짓점에 대한 텍스쳐 좌표라고 할 수 있음</p>
<pre><code class="language-cpp">float vertices[] = 
{
    //positions         //colors
     0.5f, -0.5f, 0.0f,  //1.0f, 0.0f, 0.0f, // 우하단
    -0.5f, -0.5f, 0.0f,  //0.0f, 1.0f, 0.0f, // 좌하단
     0.0f,  0.5f, 0.0f,  //0.0f, 0.0f, 1.0f, // 중앙상단
};</code></pre>
<p>따라서 아래가 위 세 삼각형 꼭짓점 좌표에 대한 텍스쳐 좌표를 매핑한거라고 할 수 있음</p>
<pre><code class="language-cpp">float texCoords[] = 
{  
    1.0f, 0.0f,  // 우하단
    0.0f, 0.0f,  // 좌하단
    0.5f, 1.0f   // 중앙상단
};</code></pre>
<blockquote>
<p>추가로 이렇게 텍스쳐 좌표를 통해 텍스처의 색상을 가져오는걸 샘플링이라고 함</p>
</blockquote>
<h1 id="텍스쳐-래핑">텍스쳐 래핑</h1>
<p>텍스쳐 좌표는 <code>0,0</code>~<code>1,1</code>까지임
근데 이걸 벗어나는 좌표가 있으면 어떻게 할거냐 이거임</p>
<p>이때 사용하는게 텍스쳐 래핑임</p>
<p><a href="https://registry.khronos.org/OpenGL-Refpages/gl4/html/glTexParameter.xhtml">glTex/TextureParameter - OpenGL Reference</a>여길 보면 옵션 개많음,,,,</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/e811db3d-d57a-447c-947e-c6443a4cc293/image.png" />
이게 옵션 종류인데,
개복잡함...</p>
<blockquote>
<p><code>glTexParameter</code> 파라미터 구조</p>
</blockquote>
<p><code>glTexParameteri(target, pname, param)</code>
<code>glTexParameterfv(target, pname, params)</code></p>
<blockquote>
</blockquote>
<ul>
<li>첫번째 : <code>target</code>
어떤 텍스처 타입에 설정할건지 지정함<ul>
<li>GL_TEXTURE_2D — 일반적인 2D 텍스처</li>
<li>GL_TEXTURE_3D — 3D 텍스처</li>
<li>GL_TEXTURE_CUBE_MAP — 큐브맵 텍스처<blockquote>
</blockquote>
사용하는 텍스처 파일의 타입에 맞춰서 사용해주면 됨<blockquote>
</blockquote>
</li>
</ul>
</li>
<li>두번째 : <code>pname</code>
텍스처의 어떤 속성을 설정할건지 지정함<ul>
<li>GL_TEXTURE_WRAP_S — S축(가로) 래핑 방식</li>
<li>GL_TEXTURE_WRAP_T — T축(세로) 래핑 방식</li>
<li>GL_TEXTURE_MIN_FILTER — 텍스처가 원래 크기보다 작게 그려질때</li>
<li>GL_TEXTURE_MAG_FILTER — 텍스처가 원래 크기보다 크게 그려질때</li>
<li>GL_TEXTURE_BORDER_COLOR — 보더 색상
등등....<blockquote>
</blockquote>
</li>
</ul>
</li>
<li>세번째 : param / params
두번째 파라미터인 pname에 대한 실제 값
위에 사진에 나와있는 옵션같은 값들임<pre><code class="language-cpp">glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_MIRRORED_REPEAT);
//              ↑ 2D 텍스처    ↑ 가로 래핑 설정   ↑ 그 값은 미러 반복</code></pre>
</li>
</ul>
<pre><code class="language-cpp">glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_MIRRORED_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_MIRRORED_REPEAT);</code></pre>
<p>위 방식대로면, 이런식으로 쓰면 되는데,,,
우리는 border에 색상까지 지정해주고 싶음</p>
<pre><code class="language-cpp">float borderColor[] = { 1.0f, 1.0f, 0.0f, 1.0f };
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColor); </code></pre>
<p>그래서 이렇게 써줌
근데 뭔가 다르지?</p>
<p><code>glTexParameterfv</code>를 사용하고 있음</p>
<blockquote>
<p><code>glTexParameteri</code> vs <code>glTexParameterfv</code> 차이</p>
</blockquote>
<p>i — 정수 하나 넘길 때. WRAP, FILTER 설정 같이 열거형 값 넘길 때 씀.
fv — float 배열 넘길 때. BORDER_COLOR처럼 색상(RGBA 4개)을 넘겨야 할 때 씀.</p>
<blockquote>
</blockquote>
<p>GL_CLAMP_TO_BORDER로 래핑 방식 자체를 설정할 때는 i를 쓰고, 
그 보더 색상까지 설정하고 싶을때, <code>glTexParameterfv</code>를 쓰는거ㅇㅇ</p>
<h1 id="텍스쳐-필터링-mipmap">텍스쳐 필터링, MipMap</h1>
<p>이 부분은 딱히 설명할게 없어서
원본 글 해석해서 적어놓음ㅇㅇ</p>
<p>단순하게 설명하면
텍스처를 축소시키는 일정 비율마다 원본 텍스처를 일정 비율(1/2, 1/4, 1/8....)로 축소한 밉맵이라는 것을 만들어두고
실제로 텍스쳐가 축소될때, 해당 비율에 도달하면 밉맵으로 저장된 텍스처로 바꿔줌
확대되는 텍스처에 대해서는 사용안함!
축소되는 텍스처에만 사용됨!</p>
<blockquote>
<h2 id="텍스처-필터링">텍스처 필터링</h2>
</blockquote>
<p>텍스처 좌표는 해상도에 의존하지 않으며 어떤 부동 소수점 값이라도 될 수 있으므로, OpenGL은 텍스처 좌표를 어떤 텍스처 픽셀(텍셀(texel)이라고도 함)에 매핑할지 계산해야 합니다. 
이는 매우 큰 객체와 저해상도 텍스처를 사용할 때 특히 중요합니다. 
이미 짐작하셨겠지만, OpenGL은 이러한 텍스처 필터링을 위한 옵션들도 제공합니다. 
여러 옵션이 존재하지만 지금은 가장 중요한 옵션인 <code>GL_NEAREST</code>와 <code>GL_LINEAR</code>에 대해 다루겠습니다.</p>
<blockquote>
</blockquote>
<ul>
<li><code>GL_NEAREST</code>(최근접 이웃 또는 포인트 필터링이라고도 함)
는 OpenGL의 기본 텍스처 필터링 방식입니다. 
<code>GL_NEAREST</code>로 설정되면, OpenGL은 중심이 텍스처 좌표와 가장 가까운 텍셀을 선택합니다. 
아래에서 십자 표시가 정확한 텍스처 좌표를 나타내는 4개의 픽셀을 볼 수 있습니다. 
왼쪽 상단의 텍셀 중심이 텍스처 좌표와 가장 가깝기 때문에 샘플링된 색상으로 선택됩니다.
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/d5db653a-6e0e-4b8f-adf9-ada41a75881e/image.png" /></li>
<li><code>GL_LINEAR</code>(선형 또는 이선형 필터링이라고도 함)
는 텍스처 좌표 주변 텍셀들로부터 보간된 값을 가져와, 텍셀 사이의 색상을 근사합니다. 
텍스처 좌표에서 텍셀 중심까지의 거리가 가까울수록 해당 텍셀의 색상이 샘플링된 색상에 더 많이 기여합니다. 
아래에서 주변 픽셀들의 혼합된 색상이 반환되는 것을 볼 수 있습니다.
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/2b20dbb7-d6f7-4c93-99ba-b5ac04fb991a/image.png" />
하지만 이러한 텍스처 필터링 방식이 시각적으로 어떤 영향을 미칠까요? 
저해상도 텍스처를 큰 객체에 사용할 때(텍스처가 확대되어 개별 텍셀이 눈에 띄게 됨) 이러한 방식이 어떻게 작동하는지 살펴보겠습니다.
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/18cec6bd-3022-4537-a4ac-d87447a407b9/image.png" />
<code>GL_NEAREST</code>는 텍스처를 구성하는 픽셀이 명확하게 보이는 블록 패턴을 생성하는 반면, <code>GL_LINEAR</code>는 개별 픽셀이 덜 보이는 더 부드러운 패턴을 생성합니다. 
<code>GL_LINEAR</code>는 더 사실적인 결과물을 만들어내지만, 일부 개발자들은 8비트 느낌을 선호하여 <code>GL_NEAREST</code> 옵션을 선택하기도 합니다.<blockquote>
</blockquote>
텍스처 필터링은 확대 및 축소 작업(확대하거나 축소할 때)에 대해 각각 설정할 수 있으므로, 예를 들어 텍스처가 축소될 때는 최근접 이웃 필터링을 사용하고 확대될 때는 선형 필터링을 사용할 수 있습니다. 
따라서 우리는 <code>glTexParameter*</code>를 통해 두 옵션 모두에 대해 필터링 방식을 지정해야 합니다. 코드는 래핑 방식을 설정하는 것과 비슷하게 작성하면 됩니다.<blockquote>
</blockquote>
<pre><code class="language-cpp">glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);</code></pre>
<blockquote>
<h2 id="밉맵mipmaps">밉맵(Mipmaps)</h2>
<p>수천 개의 객체가 있는 큰 방이 있고 각 객체에 텍스처가 붙어 있다고 상상해 보십시오. 
아주 멀리 있는 객체들도 가까이 있는 객체들과 동일한 고해상도 텍스처를 가지고 있을 것입니다. 
객체가 멀리 떨어져 있고 아마도 몇 개의 프래그먼트만 생성할 것이기 때문에, OpenGL은 고해상도 텍스처에서 해당 프래그먼트에 대한 올바른 색상 값을 검색하는 데 어려움을 겪습니다. 
텍스처의 큰 부분을 차지하는 프래그먼트에 대해 하나의 텍스처 색상을 선택해야 하기 때문입니다. 
이는 작은 객체에서 눈에 띄는 아티팩트를 생성하며, 작은 객체에 고해상도 텍스처를 사용하여 메모리 대역폭을 낭비하는 것은 말할 것도 없습니다.</p>
</blockquote>
이 문제를 해결하기 위해 OpenGL은 밉맵이라는 개념을 사용합니다. 
이는 기본적으로 후속 텍스처가 이전 텍스처보다 두 배 작은 텍스처 이미지들의 집합입니다. 
밉맵의 개념은 이해하기 쉽습니다. 
관찰자로부터 일정 거리 임계값을 지나면, OpenGL은 객체까지의 거리에 가장 적합한 다른 밉맵 텍스처를 사용합니다. 
객체가 멀리 있기 때문에 더 작은 해상도는 사용자에게 눈에 띄지 않을 것입니다. 
그러면 OpenGL은 올바른 텍셀을 샘플링할 수 있게 되고, 밉맵의 해당 부분을 샘플링할 때 캐시 메모리 사용이 줄어듭니다. 
밉맵 처리된 텍스처가 어떻게 생겼는지 자세히 살펴보겠습니다.
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/89999ee0-cbe8-4867-9852-24bd082f0f99/image.png" /><blockquote>
</blockquote>
각 텍스처 이미지에 대해 밉맵 처리된 텍스처 모음을 수동으로 만드는 것은 번거롭지만, 다행히 OpenGL은 텍스처를 생성한 후 <code>glGenerateMipmap</code>을 한 번만 호출하면 모든 작업을 대신 수행해 줍니다.<blockquote>
</blockquote>
렌더링 중에 밉맵 레벨을 전환할 때 OpenGL은 두 밉맵 레이어 사이에 날카로운 가장자리가 보이는 것과 같은 일부 아티팩트를 보여줄 수 있습니다. 
일반적인 텍스처 필터링과 마찬가지로, 밉맵 레벨 간에 전환할 때도 NEAREST 및 LINEAR 필터링을 사용하여 밉맵 레벨 간을 필터링할 수 있습니다. 
밉맵 레벨 간의 필터링 방식을 지정하기 위해 원래의 필터링 방식들을 다음 네 가지 옵션 중 하나로 대체할 수 있습니다.<blockquote>
</blockquote>
</li>
<li><code>GL_NEAREST_MIPMAP_NEAREST</code>: 픽셀 크기에 가장 가까운 밉맵을 선택하고 텍스처 샘플링을 위해 최근접 이웃 보간법을 사용합니다.</li>
<li><code>GL_LINEAR_MIPMAP_NEAREST</code>: 가장 가까운 밉맵 레벨을 선택하고 선형 보간법을 사용하여 해당 레벨을 샘플링합니다.</li>
<li><code>GL_NEAREST_MIPMAP_LINEAR</code>: 픽셀 크기와 가장 일치하는 두 밉맵 사이를 선형 보간하고, 최근접 이웃 보간법을 통해 보간된 레벨을 샘플링합니다.</li>
<li><code>GL_LINEAR_MIPMAP_LINEAR</code>: 가장 가까운 두 밉맵 사이를 선형 보간하고, 선형 보간법을 통해 보간된 레벨을 샘플링합니다.<blockquote>
</blockquote>
텍스처 필터링과 마찬가지로 <code>glTexParameteri</code>를 사용하여 필터링 방식을 위에서 언급한 4가지 방식 중 하나로 설정할 수 있습니다.<blockquote>
</blockquote>
<pre><code class="language-cpp">glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);</code></pre>
흔한 실수 중 하나는 밉맵 필터링 옵션 중 하나를 확대 필터(magnification filter)로 설정하는 것입니다. 
밉맵은 주로 텍스처가 축소될 때 사용되기 때문에 이는 아무런 효과가 없습니다. 
텍스처 확대는 밉맵을 사용하지 않으며, 여기에 밉맵 필터링 옵션을 제공하면 OpenGL에서 <code>GL_INVALID_ENUM</code> 오류 코드가 발생합니다.</li>
</ul>
<h1 id="텍스쳐-로딩-예시">텍스쳐 로딩 예시</h1>
<p>먼저 이미지 파일을 읽을수있도록 코드를 만들어야함</p>
<p>해야할 일은 단순함
이미지 확장자 별로 이미지를 읽은다음,
픽셀당 색상을 연속된 메모리 공간에 넣으면 됨</p>
<p>이때 반환타입은 <code>uint8_t*</code>, <code>unsigned char*</code>타입임</p>
<p>어? 어떠한 궁금증이 들면 아래를 읽어보셈
궁금증이 없으면 바로 쭉 스크롤해서 읽고 ㅇㅇ</p>
<blockquote>
<p>예를들어 1920*1080크기의 이미지가 있다고 가정해보자</p>
</blockquote>
<p>또한 이미지는 RGBA 혹은 RGB의 값을 가짐</p>
<blockquote>
</blockquote>
<p><code>stb_image</code>라이브러리를 이용해 이미지 파일을 로드하면 이미지의 픽셀별 색상을 계산함
<code>png</code>파일이라고 가정하면, 색상은 RGBA가 됨
그리고, 각 R,G,B,A는 0~255까지의 값을 가짐</p>
<blockquote>
</blockquote>
<p>즉, <code>stb_image</code>를 이용해 이미지를 로드하면
이미지 파일의 <code>0,0</code>의 R색상의 포인터가 반환되는거고,
메모리의 연속적인 공간에 <code>0,0</code>의 <code>R</code> ~ <code>x,y</code>의 <code>A</code>까지 저장되는거임</p>
<blockquote>
</blockquote>
<p>$$1920 \times 1080 \times 4 = 8,294,400$$바이트 $$\approx 8.3$$Mb의 메모리 공간이 필요하다는거지 ㅇㅇ</p>
<p>직접 파일 로드해서 메모리에 할당하기 귀찮으면
<code>stb_image.h</code>를 폴더에 추가하고</p>
<pre><code class="language-cpp">#define STB_IMAGE_IMPLEMENTATION
#include &quot;stb_image.h&quot;</code></pre>
<p>를 <code>stb_image.cpp</code>에 추가해서 컴파일을 하자</p>
<h2 id="texture-generate">texture generate</h2>
<p><code>glGenTextures</code>메서드를 이용해 texture ID를 만들음</p>
<p>그리고 <code>glBindTexture</code>와 위에서 설명한 <code>glTexParameter</code>메서드를 이용해 원하는 mipmap, linear, 텍스쳐 비율을 설정해주자</p>
<pre><code class="language-cpp">//텍스쳐 만들기
uint32_t texture;
glGenTextures(1, &amp;texture);
glBindTexture(GL_TEXTURE_2D, texture);

//텍스쳐 크기를 넘어가는 버텍스에 대해선 가로/세로 모두 repeat
//텍스쳐가 축소되면 mipmap linear 축소, 텍스쳐가 확대되면 linear 확대
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);    
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);</code></pre>
<p>대충 이렇게 만들음</p>
<h2 id="image-load">image load</h2>
<p><code>stb_image.h</code>라이브러리를 쓴다는 가정하에 글을 작성함</p>
<pre><code class="language-cpp">//텍스쳐 이미지 로딩
int imageWidth, imageHeight, nrChannels;
uint8_t *data = stbi_load(&quot;wood.jpg&quot;, &amp;imageWidth, &amp;imageHeight, &amp;nrChannels, 0);</code></pre>
<p>이런 코드를 작성해주자
대충 텍스쳐 이미지파일을 디렉터리에 추가하고
파일의 이름을 사용하면 됨</p>
<ul>
<li>첫번째 : 로드할 이미지 파일의 이름</li>
<li>두번째 : 이미지의 넓이</li>
<li>세번째 : 이미지의 높이</li>
<li>네번째 : 이미지의 색상 채널 갯수(e.g RGB : 3, RGBA : 4)</li>
<li>다섯번째 : 적용하고 싶은 색상 채널 갯수(0은 원본 유지, RGBA일때 3을 사용하면 RGB로 캐스팅됨)</li>
</ul>
<h2 id="image-pixel-data-to-gpu">image pixel data to GPU</h2>
<pre><code class="language-cpp">glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, imageWidth, imageHeight, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
glGenerateMipmap(GL_TEXTURE_2D);</code></pre>
<p><code>glTexImage</code>
로드한 이미지의 픽셀 데이터를 GPU로 보냄</p>
<ul>
<li>첫번째 : 사용하려는 텍스쳐의 타입</li>
<li>두번째 : mipmap의 레벨(0은 기본, 1은 가로세로 절반, 2는 그 절반...)</li>
<li>세번째 : 저장하려는 텍스쳐의 색상 포맷</li>
<li>네번째, 다섯번째 : 로드한 이미지의 넓이, 높이</li>
<li>여섯번째 : 그냥 0으로 설정(레거시 코드)</li>
<li>일곱번째 : 로드한 원본 이미지의 색상 포맷</li>
<li>여덟번째 : 로드한 이미지의 type(<code>unsigned char</code>여기선 ㅇㅇ)</li>
<li>아홉번째 : 로드한 이미지 데이터</li>
</ul>
<p><code>glGenerateMipmap</code>
밉맵을 생성하려는 텍스쳐 상태를 받아서 mipmap을 만들음</p>
<h2 id="shader-수정">shader 수정</h2>
<p><code>vertex shader</code></p>
<pre><code class="language-cpp">#version 330 core

layout (location = 0) in vec3 aPos;         //vertex position attributes
layout (location = 1) in vec3 aColor;       //vertex color attributes
layout (location = 2) in vec2 aTexCoord;    //vertex texture attributes

out vec3 ourColor; //out in connecting
out vec2 texCoord; //out in connecting

void main()
{
   gl_Position = vec4(aPos, 1.0);
   ourColor = aPos;
   texCoord = aTexCoord;
};</code></pre>
<p>일단 2d texture이미지를 받아서 out으로 다른 쉐이더에 넘겨줌</p>
<p><code>fragment shader</code></p>
<pre><code class="language-cpp">#version 330 core

out vec4 FragColor;

in vec3 ourColor; //out in connecting
in vec2 texCoord; //out in connecting

uniform sampler2D ourTex;

void main()
{
   FragColor = texture(ourTex, texCoord);

};</code></pre>
<p>in으로 texture를 받은 후,
sampler2D를 이용해서 
<code>texture</code>문법을 이용해 ourTex에 texCoord에 해당되는 픽셀 색상을 넣어서 FragColor로 넣어줌~</p>
<h2 id="전체코드">전체코드</h2>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;

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
    uint32_t texture;
    glGenTextures(1, &amp;texture);
    glBindTexture(GL_TEXTURE_2D, texture);

    //텍스쳐 크기를 넘어가는 버텍스에 대해선 가로/세로 모두 repeat
    //텍스쳐가 축소되면 mipmap linear 축소, 텍스쳐가 확대되면 linear 확대
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);    
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

    //텍스쳐 이미지 로딩
    int imageWidth, imageHeight, nrChannels;
    uint8_t *data = stbi_load(&quot;wood.jpg&quot;, &amp;imageWidth, &amp;imageHeight, &amp;nrChannels, 0);

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


    //run window
    while(!glfwWindowShouldClose(window))
    {
        process_input(window);

        glClearColor(.0f, .0f, .0f, 1.f);
        glClear(GL_COLOR_BUFFER_BIT);

        //쉐이더 사용
        shaderProgram.useShader();
        //VAO 활성화
        glBindVertexArray(VAO);
        //텍스쳐 활성화
        glBindTexture(GL_TEXTURE_2D, texture);
        // 그리기
        //glDrawArrays(GL_TRIANGLES, 0, 3);
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
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/e5f324fb-5a7b-4bd2-9290-3c86dded0af6/image.png" /></p>
<p>사진이 이런건
화면 가로세로 비율로 vertices가 정해지는데,
늘어나서 그럼ㅇㅇ</p>
<h1 id="텍스쳐-유닛">텍스쳐 유닛</h1>
<p>지금은 단일 텍스쳐만 사용중임
단일 텍스쳐만 놓고보면 텍스쳐 유닛이라는 개념은 쓸모있는 개념이 아님</p>
<p>근데 텍스쳐 여럿을 사용해야한다면 무조건 필요한 개념임</p>
<p>지금 위의 코드는 자동적으로 <code>GL_TEXTURE0</code>이라는 텍스쳐 유닛이 할당된거임</p>
<p>근데 어떨때 2개이상의 텍스쳐를 사용해야하나...?</p>
<ol>
<li>텍스쳐 블렌딩</li>
<li>같은 물체에 normal, albedo, metalic등 다양한 텍스쳐 사용할때</li>
</ol>
<p>2번에서 아!라는 탄성이 터져나왔길 바라며...</p>
<p>텍스처 유닛을 사용하는건 간단함</p>
<p><code>glActiveTexture</code>로 현재 텍스쳐 유닛을 바꿔주고,
<code>glBindTexture</code>에 지정된 텍스쳐 유닛에 원하는 텍스쳐 형식과 texture를 원하는만큼 넣어주면 됨</p>
<pre><code class="language-cpp">glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, myTex);

glActiveTexture(GL_TEXTURE0 + 1);
glBindTexture(GL_TEXTURE_2D, myTex2);</code></pre>
<p>이렇게 +를 이용해서 메모리 계산도 됨</p>
<blockquote>
<p>총 텍스쳐 유닛은 16개까지 지정 가능함
따라서 <code>GL_TEXTURE0</code>~<code>GL_TEXTURE15</code>까지 사용가능!</p>
</blockquote>
<pre><code class="language-cpp">#version 330 core

out vec4 FragColor;

in vec3 ourColor; //out in connecting
in vec2 texCoord; //out in connecting

uniform sampler2D tex1;
uniform sampler2D tex2;

void main()
{
   vec4 color1 = texture(tex1, texCoord);
   vec4 color1 = texture(tex2, texCoord);

   FragColor = mix(color1, color2, 0.2);
};</code></pre>
<p>대충 fragment shader를 위처럼 바꿔줌</p>
<p>mix를 통해 선형보간으로 두 텍스쳐를 적절히 블렌딩함</p>
<hr />
<p>전체적인 순서는 아래처럼 됨</p>
<ol>
<li>텍스쳐 1개 먼저 gen, bind, texParameter설정, 이미지로드, GPU에 로드된 이미지 넘기기 texImage2D, mipmap생성</li>
<li>나머지 텍스트도 순서대로 고고</li>
<li>창 열리기 전, shader use를 해준후 uniform값 설정<ul>
<li>각 uniform에게 미리 보고있어야 할 값 정해주기</li>
</ul>
</li>
<li>창 열린 후 glActiveTexture를 이용해서 순서대로 3번에서 정해준 값에 해당되는 텍스쳐 유닛 bind해주기</li>
<li>진행~</li>
</ol>
<h2 id="전체코드-1">전체코드</h2>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;

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

int main()
{
    constexpr int windowWidth = 1280;
    constexpr int windowHeight = 720;

    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    GLFWwindow* window = glfwCreateWindow(windowWidth, windowHeight, &quot;HukPak&quot;, nullptr, nullptr);
    if (window == NULL)
    {
        std::cout &lt;&lt; &quot;Failed to create GLFW window&quot; &lt;&lt; std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);

    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
    {
        std::cout &lt;&lt; &quot;Failed to initialize GLAD&quot; &lt;&lt; std::endl;
        return -1;
    }

    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    Shader shaderProgram(
        &quot;D:/01_Develope/CPP/learn_opengl/learn_opengl/VertexShaderSource.vsh&quot;, 
        &quot;D:/01_Develope/CPP/learn_opengl/learn_opengl/FragmentShaderSource.fsh&quot;);

    uint32_t VAO;
    glGenVertexArrays(1, &amp;VAO);
    glBindVertexArray(VAO);

    uint32_t VBO;
    glGenBuffers(1, &amp;VBO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    uint32_t EBO;
    glGenBuffers(1, &amp;EBO);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)0);
    glEnableVertexAttribArray(0);
    //color
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)(sizeof(float) * 3));
    glEnableVertexAttribArray(1);
    //텍스쳐 버텍스
    glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, sizeof(float) * 8, (void*)(sizeof(float) * 6));
    glEnableVertexAttribArray(2);

    glBindVertexArray(0);

    //텍스쳐 만들기
    uint32_t texture1, texture2;
    glGenTextures(1, &amp;texture1);
    glBindTexture(GL_TEXTURE_2D, texture1);

    //텍스쳐 크기를 넘어가는 버텍스에 대해선 가로/세로 모두 repeat
    //텍스쳐가 축소되면 mipmap linear 축소, 텍스쳐가 확대되면 linear 확대
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);    
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

    //텍스쳐 이미지 로딩
    int imageWidth, imageHeight, nrChannels;
    uint8_t *data = stbi_load(&quot;wood.jpg&quot;, &amp;imageWidth, &amp;imageHeight, &amp;nrChannels, 0);

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
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
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

        //쉐이더 사용
        shaderProgram.useShader();

        glBindVertexArray(VAO);

        // 그리기
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        glBindVertexArray(0);

        glfwSwapBuffers(window);
        glfwPollEvents();    
    }

    glDeleteBuffers(1, &amp;VBO);
    glDeleteVertexArrays(1, &amp;VAO);

    glfwTerminate();
    return 0;
}

void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}

void process_input(GLFWwindow *window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}</code></pre>
<p>주석부분만 보면 해결될거임ㅇㅇ</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/ecbc94b6-8ec4-4dd5-9e6e-777045d78f9d/image.png" /></p>
<p>근데 이미지 하나가 상하반전 되어있네?</p>
<h2 id="상하반전">상하반전</h2>
<p><code>stbi_load</code>를 하기전에 <code>stbi_set_flip_vertically_on_load(true);</code>를 해주면 해당 코드 다음부터 로드될 모든 이미지가 원하는 방향으로 flip됨</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/b8d4e23f-c775-4937-ba09-c89627e9a2d3/image.png" /></p>
<hr />
<h1 id="방향키-위아래로-텍스쳐-블렌딩-비율-수정">방향키 위아래로 텍스쳐 블렌딩 비율 수정</h1>
<pre><code class="language-cpp">#include &lt;glad/glad.h&gt;
#include &lt;GLFW/glfw3.h&gt;
#include &lt;iostream&gt;

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
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_NEAREST);    
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_NEAREST);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

    //텍스쳐 이미지 로딩
    int imageWidth, imageHeight, nrChannels;
    uint8_t *data = stbi_load(&quot;wood.jpg&quot;, &amp;imageWidth, &amp;imageHeight, &amp;nrChannels, 0);

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
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    stbi_set_flip_vertically_on_load(true); 
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

        //쉐이더 사용
        shaderProgram.useShader();
    glUniform1f(glGetUniformLocation(shaderProgram.shaderProgramID, &quot;lerp&quot;), lerp);

        //VAO 활성화
        glBindVertexArray(VAO);
        // 그리기
        //glDrawArrays(GL_TRIANGLES, 0, 3);
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
<p>중요한건
<code>glUniform1f</code>는 while문 내에서 계속 값을 수정해줘야...
값이 반영되어서 블렌딩 비율이 바뀜</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/080570cc-0e8e-4920-8ec4-a6eb6783a5f0/image.gif" /></p>