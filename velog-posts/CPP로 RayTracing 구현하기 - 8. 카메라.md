<h1 id="시야각">시야각</h1>
<p>시야각은 가로, 세로 중 어디를 기준점으로 잡느냐에 따라 좀 달라짐</p>
<p>책에서는 수직 시야각(vertical fov)를 사용한다고 함</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/a7d0e31d-c5a7-4a53-9279-e2b2a6d86cff/image.png" />
현재 우리는 Camere_center에서 focal_length가 1임</p>
<p>즉, <code>(0, 0, 0)</code>에서 z축으로 -1만큼 떨어진 뷰포트를 보는중임</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/228296d6-859f-4b27-9507-7873f0c66caf/image.png" /></p>
<p>이때 우리가 바라보는 시야의 중앙은 <code>0,0,-1</code>이고, 
이 좌표로부터 수직 시야각을 이용하여 상하로 $\theta / 2$ 만큼씩 보고있는 시야각인거임
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/caae522c-95f1-4c33-acc9-fa8608f853b4/image.png" /></p>
<p>대략 이렇게 -z축방향으로 카메라가 원점에서 $\theta$의 시야각으로 화면의 수직 방향을 보고 있는거임</p>
<p>이때 $h$는 $\theta$의 대변임</p>
<p>따라서 삼각함수 공식에 따라</p>
<blockquote>
<p>$h = \tan(\frac{\theta}{2})$ </p>
</blockquote>
<p>이 됨</p>
<p>우리는 카메라의 fov를 설정하고, 
fov를 radian으로 바꾸어 $\theta$를 만든 후, 
이를 이용해서 수직 시야범위를 계산할거임</p>
<pre><code class="language-cpp">#ifndef CAMERA_H
#define CAMERA_H

#include &quot;Color.h&quot;
#include &quot;hittable.h&quot;
#include &quot;Material.h&quot;

class Camera {
public:
    //...

    double fov          = 90; //시야각

    //...

private:
    //...

    void initialize() {
        //...

        // Camera
        //카메라부터 뷰표트까지의 거리
        double focal_length = 1.0;

        double theta = degrees_to_radians(fov);
        double h = std::tan(fov / 2);
        double viewport_height = 2 * h * focal_length;

        //...
    }

    //...
};

#endif</code></pre>
<blockquote>
<p><strong>focal_length를 곱하는 이유</strong></p>
</blockquote>
<p>비율임
h는 focal_length에 따라 비율이 변함
예를들어 focal_length가 1일때, h가 2였다면
focal_length가 4일때는 h가 8이 되어야 비율이 맞음</p>
<p>하지만 이걸로는 부족함</p>
<p>실제 카메라는 원점이 <code>0,0,0</code>도 아님
원점은 움직일 수 있고, 이로인해 바라보는 대상을 보는 각도도 바뀜</p>
<p>이를 계산해줘야함</p>
<h1 id="정규-직교-기저">정규 직교 기저</h1>
<p>기저 벡터는 n차원 공간에서 사용하는 일종의 좌표 축임</p>
<p>예를들어 2차원 공간은 <code>0,1</code>, <code>1,0</code>인 2개의 벡터로 표현할 수 있지?
이 2개의 벡터가 기저벡터임</p>
<p>그럼 3차원 공간은 뭐겠어?
<code>0,0,1</code>, <code>0,1,0</code>, <code>1,0,0</code>이 기저벡터지</p>
<p>즉, 기저벡터는 n차원 공간을 표현할 수 있는 최소개수의 벡터 집합임</p>
<blockquote>
<p><strong>정규 직교 기저</strong></p>
</blockquote>
<p>n차원 공간에서 모든 벡터의 크기가 정규화 되었으면서, 서로 수직기저 벡터들의 집합</p>
<p>이 기저벡터를 카메라의 원점을 기준으로 계산하여 카메라의 어느 방향이 오른쪽, 위쪽, 앞쪽 인지 기준점으로 삼을거임</p>
<blockquote>
<p><strong>기저벡터가 필요한 이유는 뭔데?</strong></p>
</blockquote>
<p>카메라의 방향을 지정함으로써 어디로 움직여야 어느 방향인지를 알 수 있게 하기 위함임</p>
<blockquote>
</blockquote>
<p>우리가 머리를 살짝 오른쪽으로 회전시켜보자
코를 기준으로 약 90도 오른쪽으로 회전시켰다고 가정하면...</p>
<blockquote>
</blockquote>
<p>우리가 보는 방향의 위쪽은 원래 머리 방향의 오른쪽임
우리가 보는 방향의 왼쪽은 원래 머리 방향의 위쪽임
우리가 보는 방향의 앞쪽은 원래 머리 방향의 앞쪽임</p>
<blockquote>
</blockquote>
<p>이렇게 방향에 대한 좌표가 달라지게 됨</p>
<blockquote>
</blockquote>
<p>이를 해결하기위해 원점에서부터의 기저벡터를 설정하여
해당 기저벡터를 중심으로 각도를 조절할거임</p>
<p>필요한건 다음과 같음</p>
<p><code>look from - 카메라의 위치 좌표</code>, 
<code>look at - 카메라가 보는 좌표</code>, 
<code>u,v,w - 정규 직교 기저</code>,</p>
<blockquote>
<p>u: 우측, 
v: 위쪽, 
w: 카메라에서 사용자 방향(시야에서 정면의 반대 방향), 
오른손 좌표계 기준이라, z축이 -에 있어야 보임, 
따라서 사용자는 보는 방향 반대에 위치</p>
</blockquote>
<p>추가로 필요한게 잇음
카메라의 위쪽방향에 해당되는 좌표임</p>
<p><code>vup - 카메라 기준의 위쪽 좌표</code></p>
<p>그래서 코드는 다음처럼 바뀜</p>
<pre><code class="language-cpp">class Camera {
public:
    //...
    double vfov          = 90; //수직 시야각
    Point3 look_from     = Point3(0,0,0); //카메라 원점 좌표
    Point3 look_at       = Point3(0,0,-1); //카메라 보는 곳 좌표
    Vector3 vup          = Vector3(0,1,0); //카메라 기준 위쪽 좌표

private:
    //...
    Vector3 u, v, w; //정규 직교 기저

    void initialize() {
        //...

        camera_center = look_from;

        // Camera
        //카메라부터 뷰표트까지의 거리
        double focal_length = (look_from - look_at).length();

        double theta = degrees_to_radians(vfov);
        double h = std::tan(vfov / 2);
        double viewport_height = 2 * h * focal_length;

        //해상도 비율을 사용하지 않고, 이미지를 사용하는 이유
        //해상도 이미지는 이상적인 비율이고, 실제 이미지의 비율은 해상도 비율과 다를 수 있음
        //width = height / 9 * 16 = height * 16 / 9
        double viewport_width = viewport_height * (static_cast&lt;double&gt;(image_width) / image_height);

        w = unit_vector(look_from - look_at);
        u = unit_vector(cross(w, vup));
        v = cross(w, u);

        //뷰표트의 왼-&gt;오, 상-&gt;하로 이동하는 최대 좌표 구하기
        Vector3 viewport_u = viewport_width * u;
        Vector3 viewport_v = -viewport_height * -v;

        //뷰포트의 각 픽셀간의 거리 구하기(델타 벡터)
        pixel_delta_u = viewport_u / image_width;
        pixel_delta_v = viewport_v / image_height;

        //최 좌상단 픽셀의 좌표구하기
        //camera_center - Vector3(0, 0, focal_length) : 뷰포트 바로 위에서 focal_length만큼 z방향 반대로 떨어지기
        // - viewport_u/2 - viewport_v/2 : 화면의 가로세로 절반씩 좌, 상으로 이동하기(뺄셈)
        Vector3 viewport_upper_left = camera_center - (focal_length * w) - viewport_u / 2 - viewport_v / 2;
        //위의 공식은 뷰포트의 최좌상단 좌표가 됨. 하지만 픽셀 시작 좌표는 아님
        //따라서 시작지점은 뷰포트 픽셀거리의 절반만큼 띄워진 거리에서부터 시작해야 정확히 너비 * 높이 개의 영역으로 균등하게 나눠짐
        pixel00_loc = viewport_upper_left + 0.5 * (pixel_delta_u + pixel_delta_v);
    }

    //...
};

#endif
//-------------------------------------------//
int main()
{
    HittableList world;

    auto material_ground = make_shared&lt;Lambertian&gt;(Color(0.8, 0.8, 0.0));
    auto material_center = make_shared&lt;Lambertian&gt;(Color(0.1, 0.2, 0.5));
    auto material_hollow_glass = make_shared&lt;Dielectric&gt;(1.5);
    auto material_hollow_in = make_shared&lt;Dielectric&gt;(1.00/1.5);
    auto material_diamond = make_shared&lt;Dielectric&gt;(2.417);
    auto material_right  = make_shared&lt;Metal&gt;(Color(0.8, 0.6, 0.2), 0.5);

    world.add(make_shared&lt;Sphere&gt;(Point3( 0.0, -100.5, -1.0), 100.0, material_ground));
    world.add(make_shared&lt;Sphere&gt;(Point3( 0.0,    0.0, -1.2),   0.5, material_center));
    world.add(make_shared&lt;Sphere&gt;(Point3(-1.0,    0.0, -1.0),   0.5, material_hollow_glass));
    world.add(make_shared&lt;Sphere&gt;(Point3(-1.0, 0.0, -1.0), 0.45, material_hollow_in));
    world.add(make_shared&lt;Sphere&gt;(Point3(-0.1, 0.0, -0.3), 0.1, material_diamond));

    world.add(make_shared&lt;Sphere&gt;(Point3( 1.0,    0.0, -1.0),   0.5, material_right));

    Camera cam;
    cam.aspect_ratio = 16.0 / 9.0;
    cam.image_width = 1200;
    cam.samples_per_pixel = 300;
    cam.max_depth = 50;

    cam.vfov = 20;
    cam.look_from = Point3(-2, 2, 1);
    cam.look_at = Point3(0, 0, -1);
    cam.vup = Vector3(0, 1, 0);

    cam.render(world);
}
</code></pre>
<p>그렇게 렌더링하면 아래 사진처럼 나옴</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/a0b2f63a-c037-4a36-be92-94404915ccbd/image.png" /></p>