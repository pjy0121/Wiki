# The Software Bug That Killed People: The Therac-25 Disaster

*2026-03-26*

> In the 1980s, a race condition in a radiation therapy machine killed three patients and severely injured three others. It remains the most infamous software bug in history.

## A Machine That Was Supposed to Save Lives

In 1982, a Canadian company called Atomic Energy of Canada Limited (AECL) released the Therac-25. It was the most advanced radiation therapy machine of its time. Doctors used it to treat cancer patients with precise doses of radiation. Eleven machines were installed in hospitals across the United States and Canada.

The Therac-25 had two predecessors: the Therac-6 and Therac-20. Both had excellent safety records. But there was one critical difference between them and the new model. The older machines used **hardware safety locks** — physical mechanisms that prevented dangerous radiation levels regardless of what the software did. The Therac-25 removed those hardware locks. Safety was now entirely in the hands of the software.

This decision would prove fatal.

![An early model of a medical linear accelerator from the 1950s, showing the electronic internals of the machine](https://images.unsplash.com/photo-1530497610245-b1acb28e52e1?w=700)

## When Software Fails, People Die

Between 1985 and 1987, six accidents occurred. In at least three cases, patients died. The machine delivered radiation doses **hundreds of times** greater than intended. One patient received between 15,000 and 20,000 rads when she should have received only 200. A dose of 1,000 rads can be lethal.

What made these accidents so terrifying was that nobody could figure out what was going wrong. The machine's error messages were cryptic and unhelpful — just codes like "MALFUNCTION 54." Operators had been told by the manufacturer that overdoses were impossible. So when patients screamed in pain during treatment, operators assumed it was a normal reaction. Some patients were treated again on the same malfunctioning machine.

## The Bugs Behind the Deaths

Investigators eventually found multiple software bugs:

**The Race Condition.** The Therac-25 had two modes: a low-power electron beam mode, and a high-power X-ray mode. In X-ray mode, a metal shield was positioned between the beam and the patient to safely shape the radiation. The software used a shared variable to track both the operator's input and the physical position of this shield (called a "turntable"). If a skilled operator typed commands fast enough — changing the mode within about 8 seconds — the software would switch to high-power mode but fail to move the shield into position. The patient received the full, unshielded beam.

**The Integer Overflow.** A safety check variable was stored as a single byte, meaning it could only count from 0 to 255. When it reached 255, it rolled over to 0. If, by pure chance, the operator pressed the "set" button at the exact moment the variable equaled 0, the safety check was bypassed and the beam fired without restriction.

**Poor Error Communication.** The machine displayed vague error codes and continued to appear operational even when something was critically wrong. Operators routinely ignored errors because they occurred so frequently in normal use.

![Close-up view of lines of programming code on a computer screen](https://images.unsplash.com/photo-1555066931-4365d14bab8c?w=700)

## The Root Cause Was Not Just Code

A government investigation concluded that the real problem was not any single bug. It was the entire software development process. The code had **never been independently reviewed**. AECL wrote its own operating system from scratch and tested it internally. There was no formal specification, no code review, and no systematic testing. Risk assessments unrealistically listed the probability of a software error as "extremely low."

Worse, the exact same bugs existed in the Therac-20's software. But the Therac-20 never hurt anyone because its hardware safety interlocks caught every software failure. The Therac-25 had no such backup. It trusted the software completely.

## Lessons Every Programmer Should Know

The Therac-25 disaster is now a required case study in software engineering courses around the world. It teaches several lessons that are still relevant today:

1. **Never rely on software alone for safety.** Critical systems need independent hardware safeguards. This principle, called "defense in depth," applies far beyond medical devices.

2. **Race conditions are deadly.** Concurrent programming bugs are among the hardest to find because they depend on precise timing. They may work perfectly in testing and fail in production.

3. **Reusing code does not mean reusing safety.** The Therac-25 reused software from the Therac-20, but it removed the hardware safety net that made that software safe to run.

4. **Error messages matter.** If your system shows errors that users learn to ignore, you have a design flaw. Clear, actionable error messages save lives.

5. **Independent review is not optional.** Code that can hurt people must be reviewed by someone other than the person who wrote it.

In February 1987, the FDA shut down all Therac-25 machines until permanent fixes — including hardware safety interlocks — were installed. The era of trusting software blindly with human lives was over.

Today, the Therac-25 stands as a reminder: every line of code carries responsibility. For programmers working on medical devices, self-driving cars, aviation systems, or any safety-critical software, the lesson is simple. Test thoroughly. Review independently. And never, ever assume your software is bug-free.

![Circuit board representing the intersection of hardware and software in critical systems](https://images.unsplash.com/photo-1518770660439-4636190af475?w=700)

---

# 사람을 죽인 소프트웨어 버그: Therac-25 참사

*2026-03-26*

> 1980년대, 방사선 치료 장비의 경쟁 조건(Race Condition) 버그가 환자 3명을 사망시키고 3명에게 중상을 입혔다. 역사상 가장 악명 높은 소프트웨어 버그 사건이다.

## 생명을 살리기 위한 기계

1982년, 캐나다의 원자력공사 AECL(Atomic Energy of Canada Limited)이 Therac-25를 출시했다. 당시 가장 첨단화된 방사선 치료 장비였다. 의사들은 이 장비를 사용해 암 환자에게 정밀한 방사선 치료를 시행했다. 미국과 캐나다 전역의 병원에 총 11대가 설치되었다.

Therac-25에는 전작이 두 대 있었다: Therac-6과 Therac-20이었다. 두 기종 모두 우수한 안전 기록을 보유하고 있었다. 그러나 신형 모델과의 결정적 차이가 하나 있었다. 이전 기종들은 **하드웨어 안전 잠금장치**를 사용했다. 소프트웨어가 어떤 오류를 일으키든, 물리적 메커니즘이 위험한 방사선량을 차단하는 구조였다. Therac-25는 이 하드웨어 잠금장치를 제거했다. 안전은 이제 전적으로 소프트웨어에 맡겨졌다.

이 결정은 치명적인 결과를 낳았다.

## 소프트웨어가 실패하면, 사람이 죽는다

1985년부터 1987년 사이에 6건의 사고가 발생했다. 최소 3건의 사례에서 환자가 사망했다. 장비가 의도된 것보다 **수백 배** 강한 방사선을 투여한 것이다. 한 환자는 200라드만 받아야 했는데 15,000~20,000라드를 받았다. 1,000라드면 치사량이다.

이 사고들이 더 무서웠던 이유는 원인을 아무도 파악하지 못했기 때문이다. 장비의 오류 메시지는 "MALFUNCTION 54" 같은 암호 같은 코드뿐이었다. 제조사는 운영자들에게 과선량 투여는 불가능하다고 안내했다. 그래서 환자들이 치료 중 고통으로 비명을 질러도, 운영자들은 정상적인 반응이라고 판단했다. 일부 환자는 같은 오작동 장비로 다시 치료를 받기도 했다.

## 사망의 원인이 된 버그들

조사관들은 결국 여러 소프트웨어 버그를 발견했다:

**경쟁 조건(Race Condition).** Therac-25에는 두 가지 모드가 있었다. 저출력 전자빔 모드와 고출력 X선 모드였다. X선 모드에서는 금속 차폐판("턴테이블")이 빔과 환자 사이에 위치하여 방사선을 안전하게 조절했다. 소프트웨어는 운영자의 입력값과 이 차폐판의 물리적 위치를 **하나의 공유 변수**로 추적했다. 숙련된 운영자가 약 8초 이내에 모드를 변경하며 빠르게 명령을 입력하면, 소프트웨어는 고출력 모드로 전환하면서도 차폐판을 제자리로 이동시키지 못했다. 환자는 차폐되지 않은 원시 빔을 그대로 받았다.

**정수 오버플로(Integer Overflow).** 안전 검사 변수가 1바이트로 저장되어 0부터 255까지만 셀 수 있었다. 255에 도달하면 0으로 되돌아갔다. 우연히 운영자가 변수값이 정확히 0인 찰나의 순간에 "설정" 버튼을 누르면, 안전 검사가 우회되어 빔이 제한 없이 발사되었다.

**부실한 오류 알림.** 장비는 모호한 오류 코드를 표시하면서도 정상 작동하는 것처럼 보였다. 운영자들은 정상 사용 중에도 오류가 빈번하게 발생했기 때문에 일상적으로 오류를 무시했다.

## 근본 원인은 코드만이 아니었다

정부 조사위원회는 진짜 문제가 특정 버그가 아니라 **소프트웨어 개발 프로세스 전체**에 있다고 결론 내렸다. 코드는 **독립적인 검토를 한 번도 받지 않았다.** AECL은 운영체제까지 자체 개발하고 내부에서만 테스트했다. 공식 사양서도, 코드 리뷰도, 체계적인 테스트도 없었다. 위험성 평가에서 소프트웨어 오류 확률은 "극히 낮음"으로 비현실적으로 기재되었다.

더 심각한 것은, 완전히 동일한 버그가 Therac-20의 소프트웨어에도 존재했다는 사실이다. 그러나 Therac-20은 단 한 명도 다치게 하지 않았다. 하드웨어 안전 인터록이 소프트웨어의 모든 오류를 잡아냈기 때문이다. Therac-25에는 그런 백업이 없었다. 소프트웨어를 전적으로 신뢰한 것이다.

## 모든 프로그래머가 알아야 할 교훈

Therac-25 참사는 현재 전 세계 소프트웨어 공학 과정의 필수 사례 연구이다. 오늘날에도 여전히 유효한 여러 교훈을 담고 있다:

1. **소프트웨어만으로 안전을 보장하지 말라.** 핵심 시스템에는 독립적인 하드웨어 안전장치가 필요하다. "심층 방어(defense in depth)"라 불리는 이 원칙은 의료 장비를 넘어 모든 분야에 적용된다.

2. **경쟁 조건은 치명적이다.** 동시성 프로그래밍 버그는 정확한 타이밍에 의존하기 때문에 찾기가 가장 어렵다. 테스트에서는 완벽하게 작동하다가 실제 운영 환경에서 실패할 수 있다.

3. **코드를 재사용한다고 안전까지 재사용되지 않는다.** Therac-25는 Therac-20의 소프트웨어를 재사용했지만, 그 소프트웨어를 안전하게 만들어주던 하드웨어 안전망을 제거했다.

4. **오류 메시지는 중요하다.** 사용자가 무시하는 법을 배운 오류 메시지가 있다면, 그것은 설계 결함이다. 명확하고 실행 가능한 오류 메시지가 생명을 살린다.

5. **독립적인 검토는 선택이 아니다.** 사람을 다치게 할 수 있는 코드는 반드시 작성자 외의 사람이 검토해야 한다.

1987년 2월, FDA는 영구적인 수정 — 하드웨어 안전 인터록 포함 — 이 완료될 때까지 모든 Therac-25 장비의 가동을 중단시켰다. 소프트웨어에 인간의 생명을 맹목적으로 맡기던 시대는 끝났다.

오늘날 Therac-25는 하나의 경고로 남아 있다: 모든 코드 한 줄에는 책임이 따른다. 의료 장비, 자율주행차, 항공 시스템, 또는 어떤 안전 핵심 소프트웨어를 개발하는 프로그래머에게 교훈은 간단하다. 철저히 테스트하라. 독립적으로 검토하라. 그리고 절대로, 자신의 소프트웨어에 버그가 없다고 가정하지 말라.
