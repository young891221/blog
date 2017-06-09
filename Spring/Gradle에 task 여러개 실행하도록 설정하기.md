# Gradle에 task 여러개 실행하도록 설정하기
기존에는 task를 grunt 하나만 돌리다가 이번에 webpack, grunt를 함꼐 쓰게 되면서 Gradle Build시 task를 순차적으로 모두 수행하도록 설정해 보았습니다. 

```groovy
import org.apache.tools.ant.taskdefs.condition.Os
task grunt(type:Exec) {
	workingDir "./"

	if (Os.isFamily(Os.FAMILY_WINDOWS)) {
		//on windows
		commandLine "cmd", "/c", "grunt"
	}
	else {
		//on linux
		commandLine "grunt"
	}

}
compileJava.dependsOn grunt
```
기존에 1개만 처리되는 task는 위와 같은 방식입니다. 여기서 타입을 `Exec`으로 받아서 하나만 처리하도록 설정되어 있습니다. 여기에 task를 여러개 적용하기 위해 똑같이 복붙한다고 적용되지 않습니다. 결론적으로 작동되는 소스는 다음과 같습니다. 

```groovy
import org.apache.tools.ant.taskdefs.condition.Os
task mainBuild {
	doLast {
		exec {
			workingDir "./"
			if (Os.isFamily(Os.FAMILY_WINDOWS)) {
				commandLine "cmd", "/c", "webpack -p"
			}
			else {
				commandLine "webpack -p"
			}
		}
		exec {
			workingDir "./"
			if (Os.isFamily(Os.FAMILY_WINDOWS)) {
				commandLine "cmd", "/c", "grunt"
			}
			else {
				commandLine "grunt"
			}
		}
	}
}
compileJava.dependsOn mainBuild
```
위와 같이 설정하면 task의 실행 타입을 매개변수로 받지 않고 내부에 각각 명시해 놓았습니다. 또한 `exec`은 `doFirst/doLast`안쪽에 포함되어져야 합니다. 위와 같이 설정한다면 webpack -> grunt 순으로 빌드가 수행되어 집니다. 
실행순서를 좀 더 명확하게 설정하고 싶으시다면 `shouldRunAfter`나 `mustRunAfter`를 적용하시면 됩니다.

### 참고
task에 대한 자세한 내용은 [권남님 블로그](http://kwonnam.pe.kr/wiki/gradle/task)를 참고하시면 많은 도움이 됩니다.