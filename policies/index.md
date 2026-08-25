---
layout: page
title: Policies
nav_order: 1
description: "Course policies and information."
has_children: false # the page does have children but they're all unlisted, so we explicitly set this to false to disable the drop-down arrow from appearing in the sidebar
---

# Policies
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Enrollment

[Class listing on classes.berkeley.edu](https://classes.berkeley.edu/content/2026-summer-compsci-168-001-lec-001)

In general, course staff does not control enrollment; we have to follow [the department's enrollment policies](https://eecs.berkeley.edu/resources/undergrads/cs/enrollment-policy/). We do not have any enrollment codes. If you have any questions about enrollment, please reach out to the emails listed in the enrollment policies page.

If you are a UC Berkeley student unable to enroll in the class right now, but plan to enroll later (e.g. you're waiting for a CS major declaration to be processed), please email cs168@berkeley.edu so that we know who you are and can add you to course platforms. **Please do not email us if you are a concurrent enrollment student with a pending application; you will be added automatically within 3-4 days of submitting your application.**

{% comment %} For other enrollment-related questions, please see [this FAQ page](https://su25.cs168.io/sp26-faqs/). {% endcomment %}

---

## Prerequisites


The prerequisites for this class are CS 61B and CS 61C.

The official class listing shows CS 162 as a prerequisite, but this is outdated. The correct prerequisite is CS 61C, not CS 162.

We don't formally enforce any prerequisites, and the enrollment system should not block you from enrolling or waitlisting if you haven't taken the prerequisites. We will not drop you if you haven't taken the prerequisites.

It is possible to take the class without the prerequisites, though we don't recommend it.

In addition to the prerequisite classes, we assume basic knowledge of Python and Unix systems (e.g. terminal commands).

---

## Communication

The course schedule and all resources (e.g. lecture slides, discussion worksheets) will be posted on the course website: [{{ '/' | absolute_url }}]({{ '/' | absolute_url }}).

All course announcements and content/logistics questions will happen on [Ed (the course discussion forum)]({{ site.aux_links["Ed"] }}). We will be automatically enrolling everyone.

If you need to contact the course staff privately, please make a private question on Ed or email cs168@berkeley.edu.

---

## Lecture

We will have a 90-minute live lecture on Mondays, Tuesdays, Wednesdays, Thursdays at 2:00–3:30pm in Lewis 100. This is the listed lecture time on the course schedule.

Lectures will be recorded and recordings will be posted on bCourses. We'll try to livestream lectures on Zoom, but no promises.

Lecture attendance is not required.

---

## Discussion Sections

TAs will hold 1-hour live discussions twice a week. Every discussion in the week covers the same worksheet.

You can attend any discussion sections you want. Discussion attendance is not taken.

We'll try to post recordings of discussion worksheets, but no promises.

The discussion schedule will be posted on [the course calendar]({{ "/calendar" | relative_url }}) by the first day of class.

---

## Office Hours

TAs and instructors will hold in-person and online office hours throughout the semester. We use an [online queue]({{ site.aux_links["OH Queue"] }}) to process help tickets.

The office hours schedule will be posted on [the course calendar]({{ "/calendar" | relative_url }}). Note that the schedule changes from week to week.

---

## Readings

Readings come from the [course textbook](https://textbook.cs168.io), which is freely available online. The textbook is optional, and lectures are the main source of truth in cases of ambiguity.

Another optional textbook is "Computer Networking: A Top-Down Approach, 7th edition" by Jim Kurose and Keith Ross.

---

## Exams

The midterm is on **{{ site.data.exams.midterm.date }}**.

The final exam is on **{{ site.data.exams.final.date }}**.

Please do not discuss the exam until solutions are released.

In some past iterations of CS 168, we have offered remote exams. In this semester, **we will not be offering remote exams**. All students should plan to take exams in-person at the scheduled time.

<!-- We are offering remote exams **only at the same time as the scheduled exam**. There will be no remote exams starting at any other time. Specifically, if you are taking the midterm remotely, you must start your exam at {{ site.data.exams.midterm.day }} at {{ site.data.exams.midterm.start }}. If you are taking the final exam remotely, you must start your exam at {{ site.data.exams.final.day }} at {{ site.data.exams.final.start }}.

In order to take the exams remotely, you need to agree to our video proctoring policy, which involves sending us a recording of a continuous video feed of you taking the exam. If you are not comfortable being recorded, you can always take the exam in-person.

We’ll release a form closer to the exams for you to sign up for a remote exam. -->

### Alternate Time Exams

If you are unable to take the exam at the scheduled time, we will be offering only one alternate exam time, **in-person only**. Specifically, the alternate midterm time is {{ site.data.exams.midterm.alt }}. The alternate final exam time is {{ site.data.exams.final.alt }} (starting a few minutes late for you to walk between exams). There are no other alternate exam times.

We are only offering the alternate exam time if you are unable to take the exam at the normal time. For example, if you have another exam at the same time, you can take the alternate-time exam. However, wanting a break between non-conflicting exams would not be a valid reason to take the alternate-time exam, because in this case you are able to take the exam at the normal time.

We'll release a form closer to the exams for you to sign up for an alternate-time exam.

---

## Projects

There will be 3 course projects. All projects may be done in groups of two or individually. Projects cannot be turned in late unless you have extensions or accommodations.

The projects can be submitted as often as you like before the deadline. The score you see is the score you get (there are no hidden tests). Most students receive 90%–100% of the points on projects.

If you choose to work with a partner, all work must be done synchronously in a pair-programming fashion, and you are responsible for all code submitted in the partership. If you choose to work with a partner, you are responsible for the partnership. Course staff will not help mediate partnership disputes.

---

## Grading

We will compute grades from a weighted average, as follows:

- Projects: 50% (equally weighted)
- Midterm: 25%
- Final: 25%

The class as a whole has been, and will continue to be, curved to the [department guidelines](https://eecs.berkeley.edu/resources/faculty-staff/academic-personnel/grading-guidelines-undergrad) for upper-division CS classes. Previous grade distributions on [Berkeleytime](https://berkeleytime.com/grades) are also good indicators of the curve.

---

## Ed

If you have a question, the best way to contact us is via [the course Ed site]({{ site.aux_links["Ed"] }}). The staff (instructors and TAs) will check the site regularly.

If your question is personal or not of interest to other students, please mark the question as private.

---

## LLM Policy

Use LLMs in the same way you might use a friend in the class. Just as it’s OK to ask a friend a conceptual question, you may use LLMs for conceptual questions. You would not ask a friend to write code for you, and similarly you may not ask an LLM to write code for you. As a general rule of thumb, no code or instructions should be pasted/typed into an LLM. If at any point you find yourself stuck on an assignment, concept, or anything else related to CS 168, we encourage you to find our staff at office hours or Ed! We reserve the right to call you in for random code reviews for submitted project code, with some bias towards code that appears to be LLM-generated.

## Collaboration Policy

We believe that most students can distinguish between helping other students understand course material and cheating. Explaining a subtle point from lecture or discussing course topics is an interaction that we encourage, but you must write your solutions strictly by yourself (with your partner on projects). You must not ask for project solutions on Stack Overflow or other online sites. You must not receive help on assignments from students who have taken the course in previous years, and you must not review project solutions from previous years.

If you choose to work with a partner, all work must be done synchronously in a pair-programming fashion. By creating a partnership, both you and your partner are responsible for the work in your repository and the work you submit.

Before you've submitted your final work for a project, you should never be in possession of solution code that you (or your partner) did not write. You will be equally culpable if you distribute (even unknowingly) such code to other students or future students.

You must ensure that your solutions will not be visible to other students. DO NOT GIVE ANYONE YOUR CODE! DO NOT POST SOLUTIONS TO PROJECTS ONLINE. If you use GitHub or another source control system to store your solutions electronically, you must ensure your account is configured so your solutions are not publicly visible. If you use GitHub, it offers free private repositories that allow you to keep your solutions private; please use one.

If you're not sure what you're doing is OK, please ask.

Listed below are some non-comprehensive examples of what is allowed, and disallowed.


**Permitted:**

- Discussion of approaches for solving a problem. Such help should be cited as comments in your code. For the sake of others' learning experience, we ask that you try not to give away anything juicy, and instead try to lead people to such solutions.
- Discussion of specific syntax issues and bugs in your code, without showing another student your code. Verbally discussing syntax issues is permitted, but Zoom screen sharing your code, for example, is never permitted. Cite any non course staff (course staff meaning Reader, TA, and Instructor) person you received advice from.
- Using small snippets of non-168 code that you find online for solving tiny problems such as code for iterating through a map in Python. Such usages must be explicitly cited in comments in your code.


**Absolutely Forbidden:**

- Typing or dictating code into someone else's computer.
- Looking at someone else's project code to understand a particular idea or part of a project.
- Possessing project solution code that you did not write yourself or another student's project code in any form, be it electronic or on paper. This includes the situation where you're trying to help someone debug. Distributing such code is equally forbidden.
- Posting solution code to any assignment in a public place (e.g. a public git repository, a pull request visible to all students, mediafire, etched into stones above the Mediterranean, etc). This applies even after the semester is over.
- Leaving your code in an insecure place such that other students can take the code and use it (even if it happens without your knowledge).
- Using automatic code generators such as ChatGPT and Github Copilot.
- Working in lock-step with other students. Your workflow should not involve a group of people identifying, tackling, and effectively identically solving a sequence of subproblems.
- Sharing or receiving a solution from a friend.
- Sharing or receiving a solution from a friend after the assignment is due.
- Sharing or receiving a solution from a friend after the semester is over.
- Submitting a solution from a friend to see what the autograder looks like.
- Looking at a solution that is not yours for "ideas".
- Looking at a solution on an unattended laptop.
- Publicly posting your solutions for any reason, including for your resume.

**Warning**: Your attention is drawn to the [Department's Policy on Academic Dishonesty](https://eecs.berkeley.edu/resources/students/academic-misconduct). In particular, you should be aware that copying or sharing solutions, in whole or in part, from other students in the class or any other source without acknowledgement constitutes cheating. If you are found liable for misconduct, you will receive all of these penalties:
1. Referral to the Center for Student Conduct (CSC)
2. Negative points on the entire assignment (for example, even if you copy only one line of code, you would receive negative points on the entire project)
3. Depending on severity, a failing grade in the course

If you work in a group, the penalties apply for all group members, even if only one group member engaged in misconduct, or if the other group member was unaware of the misconduct.

These policies have been consistently applied for many semesters, and in order to ensure consistency and avoid biases, we cannot reduce the penalties or make any exceptions, even if you ask us.

This policy is not a game to be defeated, and such circumventions will be seen as plagiarism.


### Reusing Code from Past Semesters

If you have completed assignments during past semesters and would like to reuse your work, you do not have to let staff know ahead of time. However:

- We cannot promise that the assignments are identical across semesters.
- By submitting work from past semesters, in part or in whole, you are claiming that the work submitted is completed entirely by you (and if applicable, your former project partner). If you are unsure if the work is your (and if applicable, your former project partner's) original work, we'd recommend redoing the assignment from scratch.
- If you intend to reuse code for a project, you are required to work alone. If you had a project partner while working on your project originally, you may submit the portions of the projects that they completed.


---

## Student Support

### Extensions Policy

We understand that life is unpredictable, and want to work with you to make sure you are supported. While we do not provide slip days in this course, please request an extension for any deadline by filling out [the extensions form]({{ site.aux_links["Extensions"] }}).

### Extenuating Circumstances & Inclusion

We recognize that our students come from varied backgrounds and have widely-varying experiences. As instructors, our goal is to teach you the material in our course. The more accessible we can make it, the better. If you encounter extenuating circumstances, please do not hesitate to let us know. The sooner we are made aware, the more options we have available to us to help.

We believe in the crucial importance of creating a learning environment that is welcoming and respectful to students of all backgrounds. The following are specific steps that will help us in achieving this goal:

- If you feel your academic performance has been impacted negatively due to a lack of inclusion, or due to experiences outside of class such as current events or family matters, please reach out to the instructors and staff. Our job is not only to teach but to support you in every way we can.
- If something is said in class (by anyone) that makes you feel uncomfortable, disrespected, or excluded by a staff member or fellow student, please report the incident so that we can work to address the issue and create a more supportive and inclusive learning environment. Some options are:
  - A departmental [Faculty Equity Advisor](https://diversity.berkeley.edu/faculty-equity-advisors), the [UC Berkeley Campus Ombuds Office](https://studentaffairs.berkeley.edu/ombuds) or the ASUC [Student Advocate's Office (SAO)](https://advocate.berkeley.edu/)
  - The [anonymous feedback form](https://engineering.berkeley.edu/about/equity-and-inclusion/feedback/) for the College of Engineering for equity and inclusion related feedback
  - The department's [Student Climate and Incident Reporting Form](https://docs.google.com/forms/d/e/1FAIpQLSc4NYHdUJ8IzYA1SoiTinWBybGWkj0mfmdnHAeygAxkZajelQ/viewform)
  - The [EECS Student Grievance Committee](https://eecs.berkeley.edu/resources/students/grievances)
- If we have inadvertently scheduled an exam or major deadline that creates a conflict with your religious observances, please let us know as soon as possible so that we can make other arrangements.
- If your name differs from your legal name, you may designate a preferred name for the classroom by following these [steps](https://registrar.berkeley.edu/academic-records/your-name-on-records-rosters/).
- Pronouns can be different for each student and should not be assumed. If you are misgendered by staff or another student, please respond however is best for you in the moment. If you're open to letting us know about the incident via email (cs168@berkeley.edu) or through the department's [Student Climate and Incident Reporting Form](https://docs.google.com/forms/d/e/1FAIpQLSc4NYHdUJ8IzYA1SoiTinWBybGWkj0mfmdnHAeygAxkZajelQ/viewform), we would really appreciate it, so we can both make sure you are appropriately addressed and continue to build habits of inclusion in our staff.
- If you're in need of laptops, Wi-Fi hotspots, or other required technologies, check out the [Student Technology Equity Program](https://studenttech.berkeley.edu/step).

### Mental Health & Wellness

As a student you may experience a range of issues that can cause barriers to learning, such as strained relationships, increased anxiety, depression, difficulty concentrating and/or lack of motivation. These mental health concerns or stressful events may lead to diminished academic performance or reduce a student's ability to participate in daily activities. UC offers services to assist you with addressing these and other concerns you may be experiencing.

If you or someone you know are suffering from any of the aforementioned conditions, consider the following services available to you:
- Counseling and Psychological Services (CAPS) has multiple free, confidential services. Walk in counseling is available with [UHS Casual Consulting](https://uhs.berkeley.edu/counseling/lets-talk). An on campus counselor or after-hours clinician is available 24/7 with [UHS Crisis Management Counseling](https://uhs.berkeley.edu/counseling/urgent).
- [UHS's mental health resources](https://uhs.berkeley.edu/health-topics/mental-health) lists additional resources.
- The National Suicide Prevention Lifeline is a 24-hour number any student or faculty/staff person can call to speak with someone about suicide: +1-800-273-TALK (+1-800-273-8255).
- Support is also available for survivors of sexual violence or harassment. While course staff are [Responsible Employees](https://svsh.berkeley.edu/responsible-employee) for such incidents, we do have free, confidential services available on campus with [UC Berkeley Sexual Violence Services](https://svsh.berkeley.edu/) and [PATH to Care Center: Reporting and Privacy](https://survivorsupport.berkeley.edu/Confidential-Resources-Anonymous-Reporting-and-Privacy).

### DSP

We committed to creating a learning environment that meets the needs of its diverse student body including students with disabilities. If you anticipate or experience any barriers to learning in this course, please feel welcome to discuss your concerns with the instructors or head TAs. All DSP and accommodations-related materials for this course are kept in a repository separate from the rest of the course materials that is visible only to staff members with the "DSP Data" tag on the course website.

If you have a disability, or think you may have a disability, you can work with the Disabled Students' Program (DSP) to request an official accommodation. The Disabled Students' Program (DSP) is the campus office responsible for authorizing disability-related academic accommodations, in cooperation with the students themselves and their instructors. You can find more information about DSP, including contact information and the application process, at [https://dsp.berkeley.edu](https://dsp.berkeley.edu/). Students registered with DSP can expect to receive an onboarding email within a week of sending us your formal letter of accommodation through the AIM portal.

### Course Climate

As a member of the Berkeley EECS community, realize that you have an important duty to help other students and staff feel respected in helping create an inclusive learning environment.

It is our expectation that all interactions with course staff and other students will demonstrate appropriate respect, consideration, and compassion for others. Please remember to be friendly and thoughtful; our community draws from a wide spectrum of valuable experiences. For further reading, please reference the [Berkeley Principles of Community](https://diversity.berkeley.edu/principles-community) and [Berkeley Campus Code of Student Conduct](https://sa.berkeley.edu/sites/default/files/Code%20of%20Conduct_January%202016.pdf).

For exceptionally rude or disrespectful behavior toward the course staff or other students, we reserve the right to file a misconduct case with the Center for Student Conduct. You don't need to be concerned about this policy if you treat other human beings with even a bare minimum of respect and consideration and do not engage in behavior that is actively harmful to others.

## Acknowledgements

Parts of the syllabus have been adapted from CS61A, CS61B, CS61C, CS161, CS188, and CS152.
