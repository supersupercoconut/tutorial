# GUI

主要表示为 openGL   glfw  imgui等部分的展示部分

```cpp
#include <iostream>
#include <string>
#include <GL/glew.h>
#include <GLFW/glfw3.h>


// 感觉这三个部分是同时使用的俄整体
#include "imgui.h"
#include "imgui_impl_glfw.h"
#include "imgui_impl_opengl3.h"

const int SCR_WIDTH = 1920;
const int SCR_HEIGHT = 1080;

int main(int argc, char **argv)
{
    // GLFW 初始化 | 并且提示与 glfw对应的opengl数据
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3); // 设置主版本号 3.x
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3); // 设置次版本号 x.3
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE); // 设置为核心模式(即现代opengl)


    GLFWwindow* window = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "StudyOpenGL", nullptr, nullptr);
    if (window == NULL)
    {
        std::cerr << "Failed to create GLFW window" << std::endl;
        glfwTerminate();
        return -1;
    }

    // 设置当前窗口为当前线程的上下文 | 虽然很拗口,但是其作用就是说后续这个线程中所有的gl操作都是针对这个窗口的
    glfwMakeContextCurrent(window);
    // 设置刷新 -- 即保证在每次电脑屏幕刷新的时候，进行更新缓冲的操作 | 用于设置同步策略 - 实际更换缓冲区还是使用 glfwSwapBuffers函数
    glfwSwapInterval(1);

    // 准备使用glew(glew在这里作用就是提供OpenGL的函数,防止由于gl版本等原因导致的问题) | 正常顺序就是完成glfw的初始化,然后再进行glew的初始化
    if(glewInit() != GLEW_OK){
        std::cerr << "Failed to initalize GLEW" << std::endl;
        return -1;
    }

    // imgui初始化
    /*
     * 1. CreateContext 创建一个imgui的上下文 | imgui上下文说明这里的操作都是针对这个imgui的操作 (这里设置上下文与glfw设置上下文还是不一样的, 两者是不一样的上下文)
     * 2. io 暂时不知道作用, 但是其一定跟输入输出有关系
     * 3. StyleColorsDark GUI中初始化的颜色风格
     * 4. ImGui_ImplGlfw_InitForOpenGL 将glfw的窗口与imgui进行绑定
     * 5. ImGui_ImplOpenGL3_Init 设置版本
     * */
    IMGUI_CHECKVERSION();
    ImGui::CreateContext(nullptr);
    ImGuiIO& io = ImGui::GetIO(); (void)io;
    ImGui::StyleColorsDark();
    ImGui_ImplGlfw_InitForOpenGL(window, true);
    ImGui_ImplOpenGL3_Init("#version 330");     // 显示版本,随手写的

    std::string text = "sad boy";
    char textbox[50] = {"Text box"};
    while (!glfwWindowShouldClose(window))
    {
        // 由于之前创建出来的数据没有被清除,所以这里需要进行清除操作 | 没有清楚会导致绘制出现的GUI界面永远保存着之前的图形 | 3D绘制的时候可能会再去清除深度信息等等
        glClear(GL_COLOR_BUFFER_BIT);
        // 每一次使用imgui渲染都需要的操作
        ImGui_ImplOpenGL3_NewFrame();
        ImGui_ImplGlfw_NewFrame();
        ImGui::NewFrame();


        // 自己创建了一个ImGUI的显示窗口，其中对应的是自己定义一些控件
        ImGui::Begin("Hello, world!");
        ImGui::Text(text.c_str());
        if(ImGui::Button("MyButton"))
        {
            text = "You clicked this button";
            std::cout << "Button Pressed" << std::endl;
        }
        // 动态读取文本数据的大小
        ImGui::InputText("Test box", textbox, IM_ARRAYSIZE(textbox));
        ImGui::End();


        // 这里的作用是显示imgui的demo窗口(相当于是官方自己写好的控件展示窗口)
        ImGui::ShowDemoWindow();

        // 渲染imgui的操作 | 让之前定义的文本、控件都显示出来的操作
        ImGui::Render();
        ImGui_ImplOpenGL3_RenderDrawData(ImGui::GetDrawData());


        /* 后续的两个函数都是常见函数 之前部分应该应该是对于这个窗windows窗口的渲染操作, 然后在之后将渲染结果显示出来(这里可以保证一直在显示)
         * 1. glfwSwapBuffers 前缓冲区和后缓冲区交换 - 可以与glfwSwapInterval联合使用
         * 2. glfwPollEvents 检查是否有触发事件(键盘或者鼠标操作)
         * */
        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    // 关闭窗口部分 | glfwTerminate清理掉glfw对应的所有资源
    glfwDestroyWindow(window);
    glfwTerminate();
    return 0;
}
```





## openGL

Immesh里面的camera类使用的并不是自己写的, 还是在learnOpenGL的教程里面看到的

关于openGL的中文教程 https://learnopengl-cn.github.io/01%20Getting%20started/01%20OpenGL/



关于shader的参考链接 https://blog.csdn.net/xueangfu/article/details/117084647?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-2-117084647-blog-98241862.235%5Ev43%5Epc_blog_bottom_relevance_base5&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-2-117084647-blog-98241862.235%5Ev43%5Epc_blog_bottom_relevance_base5&utm_relevant_index=3



着色器的作用是可以将CPU中输入，在GPU上进行运算，然后输出。着色器代表着在GPU上的运行程序，每一个着色器之前只有输入输出关系，之间没有其他通讯。

![img](figure/pipeline.png)





tinycolormap 用于颜色映射的库,其作用应该是可以按照一定的原则实现点云的上色(比如按照z轴的分布来实现点云的上色-高度越高的点其对应的颜色更深或者浅)





这里竟然Common_tools::Camera_pose_shader    g_camera_pose_shader;  但是这个camera对应的shader没有被使用,本来还以为原作者已经实现了对于整体mesh + camera的显示结果



林博这里应该只实现了两种mesh可视化部分 

1. 对于点以及片元 shader
2. 带上 image 信息的 shader

但是第二种的部分不知道具体是不是已经实现了，但是这说明在openGL中我只需要看到入门部分即可(总感觉openGL的功能是不是要被nerf或者3DGS这些东西给取代了...)





glad库的作用 - 不同厂家对于openGL的支持是不一样的(即相同功能在不同的显卡里面可能需要使用不同的openGL函数)，所以为了简化使用，即使用glad库自行管理这种对应关系。而且关于glad的使用一般是直接使用 glad.c+glad.h 文件直接使用的

 

在openGL上面怎么还要定义一个VBO 即vertice buffer object —— 主要不太懂这里的buffer是如何设置的









```cpp
/* 第一步：引入相应的库 */
#include <iostream>
using namespace std;
#define GLEW_STATIC
#include <GL/glew.h>
#include <GLFW/glfw3.h>


/* 第二步：编写顶点位置 */
GLfloat vertices_1[] =
        {
                0.0f, 0.5f, 0.0f,		// 上顶点
                -0.5f, -0.5f, 0.0f,		// 左顶点
                0.5f, -0.5f, 0.0f,		// 右顶点

        };


/* 第三步：编写顶点着色器 */
const GLchar* vertexCode_1 = "#version 330 core\n"		// 3.30版本
                             "layout(location = 0) in vec3 position_1;\n"			// 三个浮点数vector向量表示位置 position变量名
                             "void main()\n"
                             "{\n"
                             "gl_Position = vec4(position_1, 1.0f);\n"				// 核心函数(位置信息赋值)
                             "}\n";


/* 第四步：编写片元着色器(也称片段着色器) */
const GLchar* fragmentCode_1 = "#version 330 core\n"	// 版本信息
                               "out vec4 FragColor_1;\n"								//输出是四个浮点数构成的一个向量 RGB+aerfa
                               "void main()\n"
                               "{\n"
                               "FragColor_1 = vec4(0.5f, 0.75f, 0.25f, 1.0f);\n"
                               "}\n";

const GLint WIDTH = 800, HEIGHT = 600;

// DrawArray 、 VAO 这两个要重点理解，其他只要知道大致模块功能就行了。

int main()
{
    glfwInit();
    GLFWwindow* window_1 = glfwCreateWindow(WIDTH, HEIGHT, "Learn OpenGL Triangle test", nullptr, nullptr);

    int screenWidth_1, screenHeight_1;
    glfwGetFramebufferSize(window_1, &screenWidth_1, &screenHeight_1);
    glfwMakeContextCurrent(window_1);
    glewInit();


    /* 第三步：编写顶点着色器 */
    GLuint vertexShader_1 = glCreateShader(GL_VERTEX_SHADER);		// 创建顶点着色器对象
    glShaderSource(vertexShader_1, 1, &vertexCode_1, NULL);			// 将顶点着色器的内容传进来
    glCompileShader(vertexShader_1);	// 编译顶点着色器
    GLint flag;							// 用于判断编译是否成功
    GLchar infoLog[512];				// 512个字符
    glGetShaderiv(vertexShader_1, GL_COMPILE_STATUS, &flag); // 获取编译状态
    if( !flag )
    {
        glGetShaderInfoLog(vertexShader_1, 512, NULL, infoLog);   // 缓冲池
        cout<<"ERROR::SHADER::VERTEX::COMPILATION_FAILED\n"<<infoLog<<endl;
    }


    /* 第四步：编写片元着色器(也称片段着色器) */
    GLuint fragmentShader_1 = glCreateShader(GL_FRAGMENT_SHADER);		// 创建片元着色器对象
    glShaderSource(fragmentShader_1, 1, &fragmentCode_1, NULL);			// 将顶点着色器的内容传进来
    glCompileShader(fragmentShader_1);									// 编译顶点着色器
    glGetShaderiv(fragmentShader_1, GL_COMPILE_STATUS, &flag);			// 获取编译状态
    if( !flag )
    {
        glGetShaderInfoLog(fragmentShader_1, 512, NULL, infoLog);		// 缓冲池
        cout<<"ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n"<<infoLog<<endl;
    }


    /* 第五步：创建着色器程序 */
    GLuint shaderProgram_1 = glCreateProgram();
    glAttachShader(shaderProgram_1, vertexShader_1);
    glAttachShader(shaderProgram_1, fragmentShader_1);
    glLinkProgram(shaderProgram_1);
    glGetProgramiv(shaderProgram_1, GL_LINK_STATUS, &flag);
    if( !flag )
    {
        glGetProgramInfoLog(shaderProgram_1, 512, NULL, infoLog);
        cout<<"ERROR::SHADER::PROGRAM::LINKING_FAILED\n"<<infoLog<<endl;
    }
    glDeleteShader(vertexShader_1);
    glDeleteShader(fragmentShader_1);


    /* 第七步：设置顶点缓冲对象(VBO) + 第八步：设置顶点数组对象(VAO)  */
    GLuint VAO, VBO;				// 它俩是成对出现的
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);	// 从显卡中划分一个空间出来
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices_1), vertices_1, GL_STATIC_DRAW);	// GL_STATIC_DRAW：静态的画图(频繁地读)


    /* 第六步：设置链接顶点属性 */
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3*sizeof(GLfloat), (GLvoid*)0);
    glEnableVertexAttribArray(0);


    // draw loop 画图循环
    while (!glfwWindowShouldClose(window_1))
    {
        glViewport(0, 0, screenWidth_1, screenHeight_1);
        glfwPollEvents();
        glClearColor(0.0f, 0.0f, 1.0f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        /*  第九步：绘制三角形 */
        glUseProgram(shaderProgram_1);		// 渲染调用着色器程序
        glBindVertexArray(VAO);				// 绑定 VAO
        glDrawArrays(GL_TRIANGLES, 0, 3);	// 画三角形  从第0个顶点开始 一共画3次
        glBindVertexArray(0);				// 解绑定

        glfwSwapBuffers(window_1);
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteProgram(shaderProgram_1);

    glfwTerminate();
    return 0;
}

```















***

 整理immesh中关于camera_pose的跟踪序列

















