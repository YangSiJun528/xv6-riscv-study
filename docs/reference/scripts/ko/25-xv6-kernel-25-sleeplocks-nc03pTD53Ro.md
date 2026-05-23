# xv6 Kernel-25: Sleeplocks

| Field | Value |
| --- | --- |
| Video ID | `nc03pTD53Ro` |
| URL | <https://www.youtube.com/watch?v=nc03pTD53Ro> |
| Source | `docs/reference/scripts/original/25-xv6-kernel-25-sleeplocks-nc03pTD53Ro.md` |
| Status | Korean translation generated from local `original/`; review recommended |

---

## 한국어 번역

이 비디오에서는 sleeplock을 설명하고, 그것을 spinlock과 대비해서 살펴보겠습니다. 이 비디오는 xv6 운영체제 커널에 대한 시리즈의 일부입니다. 어떤 락킹 시스템이든
기본적인 함수 두 개, acquire와 release가 있습니다. 때로는 lock과 unlock이라고도 부르지만, spinlock의 경우 함수 이름이 acquire와
release이고, 둘 다 각각 spinlock을 나타내는 구조체에 대한 포인터를 인자로 받습니다.

spinlock의 핵심은, 이 락은 아주 오래 잡고 있어서는 안 된다는 점입니다. 특히 acquire 함수가 호출된 시점부터 release 함수가 실행될 때까지 그 사이에
sleep에 들어가는 것이 허용되지 않습니다. 즉, 그 사이에는 타임 슬라이싱이 없습니다. acquire 함수는 락을 얻기 위해, 또는 락이 풀리기를 기다리기 위해, 꽉 조인
루프를 돌면서(spin) 기다립니다.

이 방식은 멀티코어 시스템에서 아주 실용적인데, 왜냐하면 락은 일반적으로 다른 코어가 잡고 있고, 그 코어는 락을 오래 잡고 있을 수 없으므로 곧바로 락을 풀 것이기 때문입니다.
따라서 acquire 함수 내부의 바쁜 루프, 즉 spin 루프는 락이 풀릴 때까지 오래 기다릴 필요가 없습니다. 그래서 acquire는 결코 아주 오래 기다리지는 않습니다.

이 말은 또 무엇을 의미하냐면, release는 항상 해당 acquire를 실행했던 바로 그 코어에서 수행된다는 뜻이기도 합니다. 하지만 문제는, 락을 획득한 시점과 그것을
해제하는 시점 사이에 오래 기다려야 하는 경우입니다. 특히 acquire와 그에 대응하는 release 사이에서 sleep 함수를 호출해서 프로세스의 실행을 중단시켜야 한다면
어떻게 될까요?

만약 락을 아주 오래 잡고 있어야 한다면, spinlock에서는 다른 프로세스의 acquire가 그 긴 시간 동안 타이트한 루프를 돌면서 락이 풀리기를 기다려야 합니다. 그러면 그
코어를 사실상 완전히占유해 버리게 되고, 이런 식으로는 도저히 쓸 수가 없습니다. 대신 우리가 사용하는 것이 sleeplock입니다. sleeplock은 acquire와
release 함수가 있다는 점에서 spinlock과 매우 비슷합니다.

하지만 sleeplock에서는 락을 잡은 상태로 sleep에 들어갈 수 있습니다. xv6 구현에서 acquire 함수의 이름은 acquiresleep이고, 그에 상응하는
releasesleep이 있습니다. 이 두 함수 모두 sleeplock을 나타내는 구조체에 대한 포인터를 인자로 받습니다. 또한 initsleeplock이라는 함수가 있는데, 이
함수는 해당 구조체를 초기화하는 데 사용됩니다. 각 구조체에는 name 필드가 하나 더 있고,

이 필드는 한 번 초기화되면 변경되지 않습니다. 이 name은 디버깅할 때 사용할 수 있지만, 기능 측면에서는 중요하지 않습니다. 또 하나의 함수가 있는데, 현재 프로세스가 어떤
주어진 락을 잡고 있는지 확인하는 함수입니다. 이 함수의 이름은 holdingsleep이고, 현재 프로세스가 그 락을 잡고 있으면 true를 반환합니다. 이 함수는 주로 에러
검출을 위해 사용되며, 만약 이 함수가

true를 반환하면 커널은 즉시 panic을 일으킵니다. 자, 이제 sleeplock의 표현을 살펴봅시다. 이 구조체에는 네 개의 필드가 들어 있고, 첫 번째 필드는
spinlock입니다. 이 spinlock은 나머지 필드들을 보호하기 위해 사용됩니다. 앞에서 말했듯이 name 필드는 초기화된 뒤로는 바뀌지 않기 때문에, 엄밀히 말하면
name을 보호하는 것은 아니고, 핵심 필드인 locked라는 불리언을 보호합니다.

locked가 true이면 sleeplock이 잡혀 있는 상태입니다. 이때 pid 필드에는 실제로 그 락을 잡고 있는 프로세스의 프로세스 ID가 들어 있습니다. locked가
false이면, 락은 비어 있고 아무도 잡고 있지 않은 상태입니다. 이제 코드를 살펴보겠습니다. sleeplock.h 파일에는 sleeplock을 표현하기 위해 사용하는 구조체가
들어 있고, 그 외에는 아무것도 없습니다. 그 구조체에는 lk,

locked, name, pid 필드가 있고, 여기서 lk는 spinlock입니다. locked는 그 락이 잡혀 있을 때만 true인 불리언입니다. 그리고 name 문자열과
pid, 즉 현재 그 락을 잡고 있는(있다면) 프로세스 ID가 있는데, 이 둘은 디버깅 용도로 사용됩니다. sleeplock.c 파일에는 함수들이 들어 있습니다. 초기화 함수는 이
구조체에 대한 포인터와

name 문자열을 인자로 받고, 네 개의 필드 lk, locked, name, pid를 모두 초기화합니다. lk 필드는 spinlock이므로, 모든 spinlock을 초기화하기
위해 호출해야 하는 함수를 호출해 줍니다. 여기에는 임의의 이름을 넘깁니다. name 필드를 저장하고, locked를 이 sleeplock이 현재 잡혀 있지 않은, 즉 unlock
상태임을 나타내도록 설정합니다. 그리고 프로세스 ID도 비워 줍니다. acquire 함수는

sleeplock에 대한 포인터를 인자로 받습니다. 이 함수가 하는 일은, lk spinlock을 획득하고, locked 필드를 true로 설정해서 락이 잡혀 있음을 나타내고, 이
함수를 호출한 프로세스의 프로세스 ID를 저장한 다음, spinlock을 해제하는 것입니다. 하지만 그 전에 locked 필드를 검사해야 합니다. 왜냐하면 이 sleeplock이
현재 다른

프로세스에 의해 잡혀 있을 수도 있기 때문입니다. locked가 false이면 while 루프를 즉시 건너뜁니다. 하지만 true이면 sleep에 들어갑니다. 그리고 다시 깨어났을
때 locked 필드를 다시 검사하고, 이번에 unlock 상태라면 계속 진행할 수 있습니다. 그렇지 않으면 unlock될 때까지 계속 sleep을 반복하다가, 결국 unlock된
것을 확인하면 그때 비로소 진행합니다. 이제 sleep이 어떻게 작동하는지 기억해 봅시다. sleep은 채널과 spinlock을

인자로 받으며, sleep 함수가 호출될 때 그 spinlock은 이미 잡혀 있어야 합니다. sleep이 하는 일은, 그 spinlock을 해제하고 sleep 상태로 들어가는 것을
하나의 원자적 연산으로 수행하는 것입니다. 이렇게 하는 이유는 wakeup을 놓치지 않기 위해서입니다. 즉, 자신이 확실히 잠들었다는 것을 보장하기 전에는 spinlock을
해제하지 않습니다. 채널은 임의의 숫자로, sleep과

wakeup 함수가 그 의미를 해석하지는 않습니다. 채널은 단지 wakeup과 sleep을 조정하는 데 사용하는 값입니다. 여기서는 sleeplock의 주소를 채널로 사용합니다.
그리고 어떤 프로세스가 sleeplock을 release하면, 같은 채널 번호를 사용해 sleep 중인 모든 프로세스에게 알립니다. 즉, 그 락이 풀리기를 기다리고 있는 모든
프로세스에게 알림을 보냅니다. 그래서 우리가 다시 깨어나면, sleep

함수는 spinlock을 재획득하고, 그러면 우리는 locked 필드를 다시 검사할 수 있습니다. 여러 프로세스가 동시에 다시 깨어날 수도 있는데, 즉 여러 프로세스가 이
sleeplock을 획득하려고 시도하는 상황에서, spinlock은 그중 오직 하나에게만 넘어갑니다. 나머지는 그 spinlock을 얻기 위해 spin을 해야 합니다.
spinlock을 얻은 프로세스는 locked 필드를 검사해서 unlock되었으면 진행할 수 있습니다. 나머지 프로세스는

자신들이 spinlock을 얻었을 때, 이미 누군가가 먼저 그곳에 도달했다는 사실을 알게 됩니다. 이제 release 함수를 보겠습니다. release 함수는 spinlock을
획득한 다음, 상태를 unlock으로 설정하고, 프로세스 ID 필드를 비워 주고, spinlock을 해제합니다. 그런데 그 전에, 이 특정 sleeplock을 기다리고 있는 다른
모든 프로세스, 또는 어떤 프로세스가 있다면 그것들을 모두 깨웁니다. 마지막으로 holdingsleep

함수가 있는데, 이 함수는 디버깅에만 사용됩니다. 이 함수는 불리언을 반환하고, 만약 어떤 상황에서 false를 반환하면 우리는 panic을 호출하게 됩니다. 기본적으로 이 함수는
locked 필드를 들여다보고 그것이 설정되어 있는지 확인합니다. 그런데 locked 필드나 pid 필드를 보기 전에, 이 sleeplock을 먼저 획득해야 합니다. 그래서 이
함수는 lk sleeplock, 아니, 정확히 말하면 spinlock을 먼저 획득합니다. 즉, 이 필드들을 보기 전에 spinlock을 획득합니다.

이 두 필드를 확인하기 전에 먼저 lk 스핀락을 획득하고, 그 다음 이 필드들을 검사해서 만약 어떤 값이 설정되어 있다면 그 값을 가져와서, 현재 프로세스에 의해 잠겨 있다면
true를 반환합니다. 그리고 물론 반환하기 전에 스핀락을 해제합니다.

자, sleeplock에 대해서는 여기까지입니다. 다음 비디오에서 다시 봅시다.
