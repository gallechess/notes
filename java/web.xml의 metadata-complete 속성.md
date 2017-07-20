# web.xml의 metadata-complete 속성

## 개요
서블릿 엔진 기동 시간이 오래 걸리는 경우 web.xml에서 metadata-complete 속성을 true로 설정하라는 가이드가 많다. 이것은 웹어플리케이션이 디플로이되는 과정에서 어노테이션 스캐닝으로 인한 시간을 없애서 기동 시간을 단축하고자 할 경우인데, 이 속성에 대해서 알아본다.

## Servlet 2.5
Servlet 2.5부터 web.xml의 &lt;web-app&gt; 태그에 설정할 수 있는 metadata-complete 속성이 있다.

Servlet 2.5 명세에 제시된 설명은 다음과 같다.
> The metadata-complete attribute defines whether this deployment descriptor and other related deployment descriptors for this module (e.g., web service descriptors) are complete, or whether the class files available to this module and packaged with this application should be examined for annotations that specify deployment information.
> If metadata-complete is set to "true", the deployment tool must ignore any annotations that specify deployment information, which might be present in the class files of the application.
> If metadata-complete is not specified or is set to "false", the deployment tool must examine the class files of the application for annotations, as specified by the specifications.

서블릿 엔진이 구동될 때, 서블릿 관련 어노테이션들을 스캐닝할지 여부를 지정하는 것이다.
(true: 스캐닝 미수행, false: 스캐닝 수행, 기본값은 false)
스캐닝 대상 클래스는 서블릿 엔진에 디플로이된 클래스, 즉 웹어플리케이션 클래스로더에 로드되는 WEB-INF/classes 경로의 모든 클래스들과 WEB-INF/lib 경로의 모든 jar들이다. 이 클래스들 중에서 서블릿 엔진에 의해 라이프사이클이 관리되는 컴포넌트들에 설정된 어노테이션들이 스캐닝 여부 대상이 된다.

예를 들어, 서블릿을 개발하면서 초기화 시점에 특정 동작을 수행하고자 메소드를 하나 정의해서 @PostConstruct 어노테이션을 사용했다면, metadata-complete="true"로 지정한 경우 스캐닝되지 않으므로 해당 메소드가 동작하지 않게 된다.
물론 GenericServlet의 init() 메소드를 오버라이드한 경우라면 어노테이션 스캐닝 여부에 관계없이 동작한다.

## Servlet 3.0
Servlet 3.0부터는 서블릿 관련 어노테이션이 다수 추가되었고 web-fragment.xml 파일에 대한 스캐닝이 추가되었다. 3.0에 추가된 어노테이션들은 web.xml 파일 없이도 서블릿, 리스너, 필터 등을 개발 가능하게 해준다.

Servlet 3.0 명세에 제시된 설명은 다음과 같다.
> The web application deployment descriptor contains a metadata-complete attribute on the web-app element. The metadata-complete attribute defines whether the web.xml descriptor is complete, or whether other sources of metadata used by the deployment process should be considered. Metadata may come from the web.xml file, web-fragment.xml files, annotations on class files in WEBINF/classes, and annotations on classes in jar files in the WEB-INF/lib directory.
> If metadata-complete is set to "true", the deployment tool only examines the web.xml file and must ignore annotations such as @WebServlet, @WebFilter, and @WebListener present in the class files of the application, and must also ignore any web-fragment.xml descriptor packaged in a jar file in WEB-INF/lib.
> If the metadata-complete attribute is not specified or is set to "false", the deployment tool must examine the class files and web-fragment.xml files for metadata,as previously specified.

예를 들어, 3.0에서 @WebServlet 어노테이션으로 서블릿을 개발한 경우 web.xml에서 metadata-complete="true" 지정하면 해당 서블릿은 스캐닝 되지 않으므로 사용할 수 없다.

3.0에 web-fragment.xml 명세가 추가되면서 metadata-complete 속성을 web-fragment.xml에도 설정할 수 있게 되었다. web.xml의 설정과 다른 점은 &lt;web-fragment&gt; 태그에 설정한다는 것이며 적용 범위도 해당 jar 파일로 한정된다는 것이다. Servlet 3.0 명세에 제시된 설명은 다음과 같다.
> The web-fragment.xml also contains the metadata-complete attribute on the web-fragment element. The attribute defines whether the web-fragment.xml descriptor is complete for the given fragment, or whether it should scan for annotations in the classes in the associated jar file.
> If metadata-complete is set to “true” the deployment tool only examines the web-fragment.xml and must ignore annotations such as @WebServlet, @WebFilter and @WebListener present in the class files of the fragment.
> If metadata-complete is not specified or is set to “false” the deployment tool must examine the class files for metadata.

---

이상의 내용은 서블릿 엔진이 라이프사이클을 관리하는 컴포넌트(서블릿, 리스너, 필터 등)에만 해당된다.
즉, Spring Framework를 사용해서 정의한 bean의 초기화 메소드에 @PostConstruct 어노테이션을 썼다면, web.xml에서 metadata-complete="true"로 지정하더라도 동작에 문제는 없다. Spring bean은 서블릿 엔진이 아니라 Spring 컨테이너가 라이프사이클을 관리하기 때문.
따라서 metadata-complete 속성 변경시에는 서블릿 관련 컴포넌트들에 대해 어노테이션 스캐닝이 필요한지 위주로 검토하면 된다.
