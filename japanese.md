# 日本語翻訳：Podman, with Daniel Walsh and Brent Baude

<!-- Original text from: https://kubernetespodcast.com/episode/164-podman/ -->

## 目次

- [(5:27) Main Start](#527-Main-Start)
- [(6:54) SELinux and container security](#654-SELinux-and-container-security)
- [(9:36) Docker and History of container technologies](#936-Docker-and-History-of-container-technologies)
- [(12:42) Project Atomic](#1242-Project-Atomic)
- [(13:59) Docker issues, and to Podman](#1359-Docker-issues,-and-to-Podman)
- [(18:40) Standardize and OCI](#1840-Standardize-and-OCI)
- [(21:25) Podman features](#2125-Podman-features)
- [(28:59) Docker socket and Docker Compose](#2859-Docker-socket-and-Docker-Compose)
- [(32:02) Podman in Podman](#3202-Podman-in-Podman)
- [(33:34) Container Runtime and CRI-O](#3334-Container-Runtime-and-CRI-O)
- [(35:25) containerd and CRI-O](#3525-containerd-and-CRI-O)
- [(37:16) Desktop experience for Podman](#3716-Desktop-experience-for-Podman)
- [(42:28) Remote client for Mac](#4228-Remote-client-for-Mac)
- [(45:13) Docker adjective](#4513-Docker-adjective)
- [(47:11) Double edge sword](#4711-Double-edge-sword)
- [(48:34) Roadmap and priority](#4834-Roadmap-and-priority)
- [(49:13) Discussion with the community](#4913-Discussion-with-the-community)
- [(52:28) Podman Community](#5228-Podman-Community)
- [(53:05) Ending](#5305-Ending)


## (5:27) Main Start 
<!-- 
CRAIG BOX: Daniel Walsh is a senior distinguished engineer at Red Hat. He has worked in the computer security field for over 30 years and has been the leader of the Red Hat container engineering team since 2013.

Brent Baude is a senior principal software engineer at Red Hat. He is the architect and primary maintainer of Podman and contributed to many of its associated technologies.

Welcome to the show, Dan.

DANIEL WALSH: Thanks for having me.

CRAIG BOX: And welcome, Brent.

BRENT BAUDE: Likewise.

CRAIG BOX: Dan, your 20th anniversary at Red Hat just passed. It's hard to imagine, but the company was eight years old when you joined. Does that make you an old timer or a newbie?

DANIEL WALSH: I'd say I'm an old timer. When I joined Red Hat, there was about 400 employees, and now I think there's around 18,000 employees, not counting our parent company, IBM.

CRAIG BOX: It does seem like the kind of company that develops lifers. Are many of the people who were there at the beginning still around?

DANIEL WALSH: I think there's still probably well over 100 people that are here from when I started, so yeah. I'm slowly moving up the list of longest employees, but not that close to the top yet.

CRAIG BOX: And Brent, you joined IBM in 1997 and then left there for Red Hat after 17 years. Given the recent acquisition, does that mean that you've been there longer?

BRENT BAUDE: I do in fact tease Dan about that, but I think given how IBM has treated Red Hat as its own entity, he pulls seniority.

DANIEL WALSH: One of the great things about the acquisition is they've really treated us like a separate company.
-->

CRAIG BOX: Daniel Walshは、Red Hatのシニア・ディスティングイッシュト・エンジニアです。30年以上にわたりコンピュータセキュリティの分野で活躍し、2013年からはRed Hatのコンテナエンジニアリングチームのリーダーを務めています。

Brent Baudeは、Red Hatのシニア・プリンシパル・ソフトウェア・エンジニアです。Podmanの設計者であり、主要なメンテナでもあり、多くの関連技術に貢献しています。

番組へようこそ、Dan。

DANIEL WALSH: お招きいただきありがとうございます。

CRAIG BOX: そしてBrent、ようこそ。

BRENT BAUDE: よろしくお願いします。

CRAIG BOX: Dan、あなたがRed Hatに入社してちょうど20周年を迎えましたね。想像するのは難しいですが、あなたが入社したとき、会社は8年目でした。あなたは古株社員に当たりますか？それとも新参者？

DANIEL WALSH: 私は古株だと思います。私がRed Hatに入社したときの従業員数は約400名でしたが、現在は親会社のIBMを除いて約18,000名の従業員がいると思います。

CRAIG BOX: 生涯現役でいられるような会社だと思います。最初の頃にいた人たちは今もたくさんいるのでしょうか？

DANIEL WALSH: 私が入社したときのメンバーは、まだ100人以上はいると思いますよ。長く勤めている社員のリストには少しずつ上がってきていますが、まだトップにはそれほど近づいていません。

CRAIG BOX: Brentは1997年にIBMに入社し、17年後にRed Hatに移籍しましたね。最近の買収を考えると、Danよりもっと長く在籍していることになりますか？

BRENT BAUDE: そのことでDanをからかったこともありますが、IBMがRed Hatを独自の組織として扱ってきたことを考えると、彼のほうが年功では上だと思います。

DANIEL WALSH: (IBMによる)買収の素晴らしい点のひとつは、彼らが私たちを本当に別の会社のように扱ってくれたことです。

---
## (6:54) SELinux and container security
<!--
CRAIG BOX: One of the things, Dan, that you've been working on for a lot of that time is the security subsystem and especially SELinux. There is a website which implores people to stop disabling SELinux because it makes you sad. Are people still disabling SELinux, and are they making you sad by doing so?

DANIEL WALSH: Yes, sadly they are still disabling SELinux in a lot of places. I mean the thing about SELinux has always been that SELinux reveals the complexity of the operating system, so getting SELinux right and getting your applications right is sometimes difficult.

The beauty of containers, though, is that the container world is sort of simplified in the operating system, in that instead of having a web server talking directly to a database server, you have a web service container talking to a database container. And therefore, we basically put these applications into a box, and then we can control the communication pass between the two different applications.

And so containers and SELinux are sort of a perfect match, simplifying the operating system down to individual sandboxed applications that SELinux is a great tool for protecting. It really protects the file system from the containers.

So, for most vulnerabilities in container worlds have been basically file system attacks, right? The container breaking out of its namespaces or breaking out to actually see the operating system underneath it. And then SELinux has been, in my opinion, the best tool for controlling what goes on in the operating system, basically those accesses. So whether or not the container process can read your credit card data in your home directory, and those are all blocked by SELinux.

CRAIG BOX: Is it still something that the people who are writing and running the applications need to think about, or is it now just an implementation detail? In your Kubernetes manifest, you say enable security and SELinux is the system that makes that happen.

DANIEL WALSH: The only place where SELinux tends to come up in container world is when you start to leak host information into the container, so really volumes. So if you mount a volume into a container, you need to make sure that basically, SELinux policy says the container process type can only read and write container file types.

So if you take directory out of your home directory and stick it in via volume, then you have to make sure that the labeling is correct. And what we did way back when I was working on Docker is we came up with the colon Z or colon "zed" for the foreigners…

CRAIG BOX: Thank you for translating for me.

DANIEL WALSH: Capital Z and lower case Z basically tell the container engine Docker or Podman to relabel. Basically, set the content of the volume that you're sticking in to match the container label. That's really the only time SELinux tends to come in to an issue in the container world.
-->

CRAIG BOX: Danがその間ずっと取り組んできたことのひとつに、セキュリティサブシステム、特にSELinuxがありますね。あるウェブサイトでは、SELinuxを無効にすると悲しくなるからやめてほしいと訴えています。今でも人々はSELinuxを無効にしていて、それによってあなたを悲しませているのでしょうか？

DANIEL WALSH: はい、悲しいことに、まだ多くの場所でSELinuxを無効にしています。SELinuxはOSの複雑さを明らかにしてしまうので、SELinuxを正しく設定することと、アプリケーションを正しく設定することは時に困難です。

しかし、コンテナの良さは、コンテナの世界がオペレーティングシステムの中で単純化されていることです。つまり、ウェブサーバーがデータベースサーバーと直接対話するのではなく、ウェブサービスコンテナがデータベースコンテナと対話するのです。そのため、基本的にはこれらのアプリケーションを箱の中に入れ、2つの異なるアプリケーション間の通信パスを制御することができるようになります。

このように、コンテナとSELinuxは完璧にマッチしており、OSをサンドボックス化された個々のアプリケーションにまで簡素化することができますが、SELinuxはその保護に優れたツールです。SELinuxは、ファイルシステムをコンテナから保護する優れたツールなのです。

コンテナの世界における脆弱性のほとんどは、基本的にはファイルシステムへの攻撃ですよね。コンテナが自分の名前空間から抜け出したり、その下にあるオペレーティングシステムを見るために抜け出したりするのです。私の意見では、SELinuxはオペレーティングシステムで行われること、つまり基本的にはこれらのアクセスを制御するための最良のツールとなります。つまり、コンテナプロセスがホームディレクトリにあるあなたのクレジットカードデータを読めるかどうか、それらはすべてSELinuxによってブロックされているのです。

CRAIG BOX: セキュリティはアプリケーションを書いて動かしている人たちが考えなければならないことなのか、それとも、今では単なる実装上の詳細なのでしょうか？Kubernetesのマニフェストにはenable securityとありますが、SELinuxはそれを実現するためのシステムです。

DANIEL WALSH: コンテナの世界でSELinuxの出番があるのは、ホスト情報をコンテナに公開するときだけで、実際にはボリュームですね。ボリュームをコンテナにマウントする場合、基本的にSELinuxのポリシーでは、コンテナのプロセスタイプはコンテナのファイルタイプしか読み書きできないため、そのことを確認する必要があります。

ですから、ホームディレクトリからディレクトリを取り出して、ボリューム経由で(コンテナ内に)貼り付ける場合は、ラベル付けが正しいことを確認する必要があります。私がDockerに取り組んでいた頃、私たちは外部からの受け入れのために「:Z」または「:z」[^1]を考え出しました...。

CRAIG BOX: 私のために翻訳してくれてありがとう。

DANIEL WALSH: 大文字のZと小文字のZは、基本的にコンテナエンジンのDockerやPodmanにラベルを張り替えるように指示します。基本的には、コンテナのラベルに合わせて、割り当てているボリュームの内容を設定します。コンテナの世界でSELinuxが問題になりがちなのは、本当にこの点だけです。

---
## (9:36) Docker and History of container technologies
<!--
CRAIG BOX: Now, I've been made aware that confusing Docker and containers and saying one when you mean the other is a swear jar-worthy offense on your team.

DANIEL WALSH: [CHUCKLES] You've been doing your research!

CRAIG BOX: I do want to check in on that. A lot of people will consider Docker and containers to be analogous because they've come to them in the past five years where that has largely been true in terms of public opinion. When you started in 2013, that was before Docker. What did it mean to lead container engineering at that time?

DANIEL WALSH: Going back in my career, I've been working on what I would call container technology, although we didn't use the word container. We used sandboxes. All the way back to basically around 2006, I was working on SELinux at the time. But what we were trying to do is to create these controlled sandboxed environments. And we were introducing into the kernel things like different namespaces. The original one was the mount namespace.

And later, when RHEL 6 came out, I created a thing called the SELinux sandbox. The goal there was to create multiple home directories so that you could run two different versions of Firefox or a web browser, where one would see the internet and the one would see your intranet.

And then you isolate those two from each other. And what I was doing there was using mount namespace to create different home directories inside, and then using SELinux to control what was going, basically, access outside. I wasn't smart enough to call this thing a container, but that actually existed way back then.

CRAIG BOX: That's branding. You needed a marketing team for that.

DANIEL WALSH: Exactly. I also worked on the original version of OpenShift, which was a web based service where we would give out accounts basically for free. You give us an email address, and we would give you an account on Red Hat services, and you were allowed to develop applications.

Well, obviously that's opening up a world where we had lots of hostile users getting accounts, and we had to control those accounts. And again, we used SELinux to control those accounts.

Around 2013, we were looking at increasing OpenShift's controls, basically adding in a lot of the namespace stuff that was in the Linux kernel. We created a thing called OpenShift gears. And these things were like containers, basically. So we were working heavily on that. I was working on another thing called virt-sandbox, virtualization sandbox.

That's when Docker sort of became the hot thing on the internet, so we pivoted inside of OpenShift and started looking at Docker as basically this new hot technology that everybody in the open source world wanted to use. And very quickly we wanted to get that into RHEL and into OpenShift.

And that's when I started leading the team that was basically making Docker what we call enterprise ready. So we added all sorts of features to Docker. Docker at that time was using a file system that was not part of the upstream kernel. So people on my team added device mapper support and later overlay support. We also added SELinux support to Docker and things like that.

And so for the first couple of years, we worked heavily on what I would say is making Docker enterprise quality so that we could ship it and support it inside of RHEL.
-->

CRAIG BOX: さて、私が調べたところ、Dockerとコンテナを混同したり、他の意味で言っているのに一方を言ったりすることは、あなたのチームでは誓いの瓶(Swear jar)に相当する悪口のようですが。

DANIEL WALSH: [笑い声] よく調べてますね！

CRAIG BOX: その点について確認したいことがあります。多くの人がDockerとコンテナを類似のものと考えているでしょう。というのも世論的にはこの5年間で大きく広まったからです。あなたが2013年に入社したときは、Dockerが広まる前でした。当時、コンテナエンジニアリングをリードするということはどういうことだったのでしょうか？

DANIEL WALSH: 私のキャリアを振り返ると、コンテナという言葉は使っていませんでしたが、コンテナ技術と呼ばれるものに取り組んでいました。サンドボックスを使っていました。2006年頃までさかのぼりますが、当時はSELinuxの研究をしていました。しかし、私たちがやろうとしていたのは、制御されたサンドボックス環境を作ることでした。そして、カーネルに異なる名前空間のようなものを導入していました。最初のものは「mount namespace」でした。

その後、RHEL 6が登場したとき、私はSELinux sandboxというものを作りました。その目的は、複数のホームディレクトリを作成し、2つの異なるバージョンのFirefoxやWebブラウザを実行できるようにすることでしたが、1つはインターネットを、もう1つはイントラネットを見ることができます。

そして、その2つをお互いに分離するのです。そこで私がやっていたのは、mount namespaceを使って、内部に異なるホームディレクトリを作り、SELinuxを使って、基本的に外部からのアクセスを制御することでした。私はこれをコンテナと呼ぶほど賢くはありませんでしたが、当時は実際に存在していたのです。

CRAIG BOX: これはブランディングです。そのためのマーケティングチームが必要だったのですね。

DANIEL WALSH: その通りです。また、私はOpenShiftのオリジナルバージョンにも携わりました。これはウェブベースのサービスで、基本的に無料でアカウントを提供していました。メールアドレスを提供すると、Red Hatサービスのアカウントが与えられ、アプリケーションを開発することができました。

これは明らかに、多くの敵対的なユーザーがアカウントを取得する世界を開くものであり、私たちはそれらのアカウントを管理しなければなりませんでした。そこで、SELinuxを使ってアカウントを管理しています。

2013年頃、私たちはOpenShiftの制御を強化することを検討していました。基本的には、Linuxカーネルにある多くの名前空間を追加しました。そこで、「OpenShift gears」というものを作りました。これは、基本的にはコンテナのようなものです。私たちはこれに力を入れていました。私はvirt-sandbox（Virtualization Sandbox）というものにも取り組んでいました。

その頃、Dockerがインターネット上で話題になっていたので、私たちはOpenShiftの内部に軸足を移し、Dockerをオープンソースの世界で誰もが使いたいと思っている新しいホットなテクノロジーとして見始めました。そして、すぐにRHELとOpenShiftにDockerを導入しようとしました。

そこで私は、Dockerをいわゆるエンタープライズ対応にするためのチームを率い始めたのです。そこで私たちはDockerにあらゆる種類の機能を追加しました。当時のDockerは、アップストリームのカーネルに含まれていないファイルシステムを使用していました。そこで、私のチームの人たちが「device mapper」のサポートを追加し、後には「overlay」のサポートも追加しました。また、DockerにSELinuxのサポートを追加したりしました。

最初の2、3年は、RHELの中でDockerを出荷してサポートできるように、Dockerをエンタープライズ品質にすることに力を注ぎました。

---
## (12:42) Project Atomic
<!--
CRAIG BOX: Brent, your last couple of years at IBM were actually working with Red Hat. You joined the containers team around the same time in June 2014. What were some of the first things that you worked on?

BRENT BAUDE: We worked on the Atomic project, which, Dan was there as well, and primarily, Dan and I at that time were working on a tool called Atomic that was Python-based for, believe it or not, managing containers and adding some specific features to Atomic that weren’t present in most container runtimes.

CRAIG BOX: I remember Atomic as being the name of a distribution, which was sort of a cut down version of RHEL designed to be the base for containers. Was that the thing that came later?

BRENT BAUDE: I think it was hand in hand, if I remember correctly.

DANIEL WALSH: Yeah, Atomic was basically a tool that ran on top of basically the Project Atomic operating system. Eventually, when Red Hat purchased CoreOS, Project Atomic and CoreOS sort of merged. The goal was to take the best technologies out of CoreOS and the best technologies out of Atomic and merge them together.

What we were experimenting with the Atomic command line was to advance features that at the time Docker didn't have and things that we thought were necessary, things like scanning and tools like that to look at images and to enhance on top of it.
-->

CRAIG BOX: Brent、あなたのIBMでの最後の数年間は、実はRed Hatで働いていました。2014年6月、同じ時期にコンテナチームに参加しましたね。最初に取り組んだことは何でしたか？

BRENT BAUDE: 私たちはAtomicプロジェクトに取り組んでいました。Danもそこにいましたが、当時Danと私は、信じられないかもしれませんが、コンテナを管理するためのPythonベースのAtomicというツールと、ほとんどのコンテナランタイムにはない特定の機能をAtomicに追加することに取り組んでいました。

CRAIG BOX: Atomicはディストリビューションの名前だったと記憶していますが、それはコンテナのベースとして設計されたRHELの縮小版のようなものでした。それは後から出てきたものですか？

BRENT BAUDE: 私の記憶が正しければ、密接な関係にあったと思います。

DANIEL WALSH: Atomicは、基本的にはProject Atomicオペレーティングシステムの上で動作するツールでした。最終的には、Red HatがCoreOSを買収したことで、Project AtomicとCoreOSが統合されました。その目的は、CoreOSの優れた技術とAtomicの優れた技術を取り出して、それらを統合することでした。

Atomicコマンドラインで実験していたのは、当時のDockerにはなかった機能や、必要だと思っていた機能、つまりスキャンやイメージを見るためのツールなどを進化させ、その上で機能を強化することでした。

---
## (13:59) Docker issues, and to Podman
<!--
CRAIG BOX: I think that may well end up being the theme of a lot of the things you've worked on here. You said that you were working with the Docker team and building enterprise features in. But then there are a lot of things that you've built which are substitutions or complementary to Docker itself. When would you decide to build something yourself versus enhancing the upstream product?

DANIEL WALSH: We continued to work on the upstream projects, but we quickly found that some of our ideas started to conflict with what Docker wanted to do. For instance, we wanted to support multiple registries. Docker wanted to hide code. Docker.io was the only way to use registries.

Slowly, over time, we had our own version of Docker with lots and lots of patches. Then we were trying to get those patches upstream, but Docker was hostile to them. We also wanted to get better systemd support. A lot of our users wanted to be able to run systemd inside of a container. Upstream Docker was fully hostile to a lot of these patches.

So slowly over time, there would seem to be this conflict going on at all times. Docker was a huge success, but I also had a problem with their whole design. The way I look at Docker is they had a tool similar to OpenShift. It was really a REST API tool, and then they built the Docker daemon based on that.

So you took a lot of engineers who were really looking at developing web applications, and they built an application that was really a client server application. So a lot of people don't understand that when you run Docker, it is a client that talks over REST API to a Docker daemon, a root running daemon. It asks it to launch containers.

So the container is not a child of Docker CLI. It's actually a child of the daemon. And I always looked at container processes as just regular processes on the system, so why couldn't I just have a tool that just launched a container as a child? Most of the engineers from Red Hat come from an operating system point of view, fork-exec model where process is a child of a process.

So we started to look at it and said, why do I need these daemons? And if you looked at how Docker was evolving at the time, Docker was originally just a daemon. Then Docker was a daemon with — they had these plugin volumes, and those were daemons. And then they had these security authorization daemons. And there's this proliferation of these root running demons just spreading all over.

Eventually, Docker even split into Docker and containerd as two different daemons. So there's always this proliferation of daemons, and I always kept on coming back to — these are just processes. Why do we have to do all this stuff?

So we started, basically, a skunkworks project to figure out could we basically take some of the technology that we had given to the Docker project and pull some of it out and just look at, could we just do these containers without having to always talk to a daemon?

And so we started working on things like Podman. Then we wanted to do something that was dedicated to Kubernetes, and that was with CRI-O. And then we wanted to do some kind of tool to build container images that didn't need to talk to a daemon, and that was Buildah.

We took apart what Docker did into its core components and built a library to do the storage of images and as container storage. Then we built a library that got all those containers' image, which does the pulling and pushing of images back and forth to container registries.

Made those separate libraries, and then we started building tools on top of those. And out of those came three tools I just mentioned — Buildah, CRI-O, and Podman, and also a fourth tool we called Skopeo, which was just really a tool for copying images between different types of container storage.

All of these share the same underlying libraries and share the same underlying content. Podman can use a Buildah container, and CRI-O can use Podman containers and build their images. We started to build up the tools separately, and in my opinion, more like a Unix model where each application does one thing and does it very well.

Obviously, that's where Podman became sort of a glue code, and we had to have something that worked like Docker. The one thing I didn't want to have when we were first talking about designing it is — Google knows how to run containers, and that involves this Docker command line tool. So how do I take a directory and stick it in a container? I do a run -v.

We decided very early on that all of the Podman commands had to match exactly the Docker commands. So the only thing you had to do was change D-O-C-K-E-R to P-O-D-M-A-N, and everything would work correctly.

CRAIG BOX: All the examples even still line up in the monospaced font.

DANIEL WALSH: Yeah, exactly. That's a reason because Google memory is very long, and we were never going to be able to do that. And I consider Podman to be the Pepsi to Docker's Coke. That's the way we want to go.
-->

CRAIG BOX: それが、あなたがここで取り組んできた多くのことのテーマになるのではないかと思います。あなたは、Dockerチームと協力してエンタープライズ機能を構築していると言いました。しかし、あなたが作ったものの中には、Docker自体の代わりになったり、補完したりするものがたくさんあります。どのような場合に、アップストリーム製品を強化するのではなく、自分で何かを構築することを決定するのでしょうか？

DANIEL WALSH: 私たちはアップストリームのプロジェクトに取り組み続けましたが、すぐに私たちのアイデアのいくつかがDockerがやりたいことと対立し始めたことに気づきました。例えば、私たちは複数のレジストリーをサポートしたいと考えていました。Docker社はコードを隠したかったのです。Docker.ioはレジストリーを使用する唯一の方法でした。

ゆっくりと時間をかけて、私たちはたくさんのパッチを使って独自のDockerバージョンを作りました。そして、それらのパッチをアップストリームに流そうとしたのですが、Dockerはそれらを敵視していました。また、systemdのサポートも充実させたいと思っていました。多くのユーザーがコンテナ内でsystemdを実行できるようにしたいと考えていました。アップストリームのDockerは、これらのパッチの多くを完全に敵視していました。

そのため、ゆっくりと時間をかけて、常にこのような対立が起こっているように見えました。Dockerは大成功を収めましたが、私はそのデザイン全体に問題を感じていました。私がDockerをどう見ているかというと、彼らはOpenShiftと似たようなツールを持っていました。それは実際にはREST APIのツールで、それをベースにしてDockerデーモンを作ったのです。

つまり、ウェブアプリケーションを開発しようとしていた多くのエンジニアを集めて、実際にはクライアントサーバー型のアプリケーションを作ったわけです。多くの人が理解していないのですが、Dockerを実行するとき、それはREST APIを介してDockerデーモン（ルートで動作するデーモン）と対話するクライアントがコンテナを起動するように要求するのです。

ですから、コンテナはDocker CLIの子ではありません。実際にはデーモンの子供なのです。私は常にコンテナプロセスをシステム上の通常のプロセスとして見ていたので、なぜコンテナを子として起動するだけのツールがないのか？と疑問を持っていました。Red Hatのエンジニアの多くはOSの観点から来ており、プロセスがプロセスの子である「fork-execモデル」で見ています。

そこで私たちは、「なぜこのようなデーモンが必要なのか」ということを考え始めました。当時、Dockerがどのように進化していたかというと、Dockerはもともと単なるデーモンでした。その後、ボリュームのプラグインができ、それらがデーモンとなりました。そして、セキュリティ認証のデーモンもできました。そして、これらのルート・ランニング・デーモンが増殖し、あちこちに広がっていきました。

最終的には、DockerはDockerとcontainerdという2つの異なるデーモンに分かれました。このようにデーモンは常に増殖していて、私はいつも「これらは単なるプロセスだ」ということに立ち返っていました。なぜこんなことをしなければならないのか？と。

そこで私たちは、Dockerプロジェクトに提供した技術の一部を取り出して、常にデーモンとやり取りすることなくコンテナを実行できないか、というスカンクワークプロジェクトを始めました。

そこで、Podmanのようなものに取り組み始めました。その後、Kubernetesに特化したものを作りたいと考え、CRI-Oを設立しました。そして、デーモンと会話する必要のないコンテナイメージを構築するためのツールを作りたいと思い、それがBuildahとなりました。

私たちは、Dockerが行っていたことをコアコンポーネントに分解し、イメージの保存とコンテナの保存を行うライブラリを構築しました。また、コンテナのイメージを取得するライブラリを構築し、コンテナレジストリとの間でイメージのやり取りを行いました。

このように別々のライブラリを作り、その上にツールを作り始めました。その結果、Buildah、CRI-O、Podmanの3つのツールと、Skopeoと呼ばれる4つ目のツールが誕生しました。

これらのツールはすべて、基礎となるライブラリを共有し、基礎となるコンテンツを共有しています。PodmanはBuildahのコンテナを使うことができ、CRI-OはPodmanのコンテナを使ってイメージを構築することができます。私たちは、ツールを別々に構築し始めました。私の考えでは、それぞれのアプリケーションが1つのことを行い、それを非常にうまくこなすというUnixモデルに近いものでした。

明らかに、そこにおいてはPodmanが「Glue code（互換性がない部分同士を結合するためだけに働くコード）」のようなものとなり、Dockerのように機能するものが必要になりました。最初に設計の話をしていたときに嫌だったのは、Googleはコンテナを実行する方法を知っていて、それにはDockerコマンドラインツールが必要だということでした。「Q: ディレクトリをコンテナに入れる方法は？」「Google Answer: run -vを実行します。」

PodmanのコマンドはすべてDockerのコマンドと完全に一致しなければならないと、かなり早い段階で決めました。つまり、D-O-C-K-E-RをP-O-D-M-A-Nに変更するだけで、すべてが正しく動作するというような。

CRAIG BOX: 例題もすべて等幅フォントで表示されていますね。

DANIEL WALSH: ええ、その通りです。それは、Googleのウェブインデックス期間が非常に長いのが理由ですが、そのようにするつもりはありませんでした。つまりPodmanはDockerのコカ・コーラに対するペプシだと思っています。それが私たちのやりたいことです。

---
## (18:40) Standardize and OCI
<!--
CRAIG BOX: Around that time as well, Docker were reticent to standardize their container image spec or their runtime spec, and it really took the appC project coming out of CoreOS to get everyone else to align around a Pepsi, if you will, such that Coke decided that they needed to publish their secret ingredients. I'm not sure quite how far we can strain this metaphor. Was this all around the same time?

DANIEL WALSH: CoreOS was still a separate company at that time. They weren't part of Red Hat. CoreOS had developed the rocket package, and they were trying to push that as an alternative way of running containers. At that time, everybody called them Docker images, but it had a way of describing a container image.

And really, a container image is nothing more than create a rootfs, basically something that looks like root on a Linux operating system. You put some content into it. You tar it up. Then you create a JSON file associated with that tarball, and the JSON file has sort of the fields that you see in a Docker file, the environmental variables, the maintainer, the commands to be run inside of the container, and you combine all that together. And that's really what a container image is.

The problem is that that JSON file wasn't standardized, so any time Docker wanted, they could go on and change the standard of the JSON file. Coming from my long history in Linux and Unix, having Microsoft controlling the .doc format has not been a great thing for the world. Well, it's been a great thing for Microsoft, but back in the '90s, it wasn't a great thing.

So there was a push to standardize, basically get a standard, so that people couldn't make arbitrary changes to force everybody to upgrade their tooling and pay more money for upgrades, similar to what Microsoft had done over the years. So when CoreOS came out with this standard, Red Hat sort of agreed this shouldn't be controlled by one company. There should be a standards body for controlling what the format of these images are.

And the other fear was, if you go way back in the Linux world 25 years ago, RPM and Debian format came out. And suddenly, forever, we had two ways of shipping software.

In the Linux world, anybody that wanted to ship an application had a package as RPM, a package as DEB. With container worlds, there was container images. And what we didn't want to have is two ways of everybody ending up having to ship images in two different formats.

So that's where, really, OCI came about, and OCI obviously allowed us to really begin to standardize. Right now, all of our tools run basically the exact same OCI images that Docker runs, and we create the exact same images.

So the input and the output, the way we run containers is exactly the same as Docker. And the way the images that we use to run containers are exact, and those are standardized inside of the Open Container Initiative, OCI.
-->

CRAIG BOX: その頃、Dockerはコンテナイメージの仕様やランタイムの仕様を標準化することに難色を示していましたが、CoreOSのappCプロジェクト[^2]によって、コカ・コーラが秘密の成分を公開する必要があると判断するような、いわばペプシのようなものをみんなが揃えることになったのです。この比喩がどこまで通用するかはわかりませんが。これは同時期の出来事ですか？

DANIEL WALSH: 当時、CoreOSはまだ別の会社でした。Red Hatの傘下には入っていませんでした。CoreOSは「rkt」パッケージを開発していて、コンテナを実行する代替手段としてそれを推し進めようとしていました。当時、誰もがDockerイメージと呼んでいましたが、コンテナイメージを表現する方法があったのです。

コンテナイメージとは、基本的にはLinux OSのrootのようなものであるrootfsを作成することにほかなりません。そこに何かコンテンツを入れます。それをtarで固めます。JSONファイルには、Dockerファイルにあるようなフィールド、例えば環境変数、メンテナー、コンテナ内で実行するコマンドなどが含まれており、これらをすべて組み合わせます。これがコンテナイメージの正体です。

問題は、そのJSONファイルが標準化されていないことで、Dockerはいつでも好きな時にJSONファイルの標準自体を変更することができました。私は長い間LinuxやUnixに携わってきましたがその視点から見て、Microsoftが.docフォーマットをコントロールすることは、世界にとってあまり良いことではありませんでした。まあ、Microsoftにとっては素晴らしいことですが、90年代には素晴らしいことではありませんでした。

そこで、Microsoftが長年行ってきたのと同じようにした結果、ユーザーが勝手な変更をしてツールをアップグレードしたり、アップグレードのためにお金を払わなければならないようなことがないように、標準化を進めようという動きがありました。そこで、CoreOSがこの規格を発表したとき、Red Hatはこの規格を一企業が管理すべきではないと考えました。イメージのフォーマットを管理するための標準化団体を作るべきです。

もうひとつの懸念は、25年前のLinuxの世界を振り返ってみると、RPMとDebianのフォーマットが登場したことです。すると突然、ソフトウェアを配布する方法が2つになったのです。

Linuxの世界では、アプリケーションを配布しようとする人は、RPMとしてのパッケージとDEBとしてのパッケージを用意する必要があります。コンテナの世界では、コンテナイメージがありましたが、2つの方法があって2つの異なるフォーマットのイメージを配布しなければいけないということにはしたくありませんでした。

そこで登場したのがOCIです。OCIによって、私たちは本当に標準化を始めることができました。現在では、私たちのすべてのツールは、基本的にDockerが実行するのとまったく同じOCIイメージを実行し、まったく同じイメージを作成しています。

つまり、入力も出力も、コンテナの動かし方も、Dockerとまったく同じなのです。また、コンテナを実行するためのイメージも全く同じで、それらはOpen Container Initiative（OCI）の中で標準化されています。

---
## (21:25) Podman features
<!--
CRAIG BOX: A lot of people will come to this episode with the thought in the back of their head that Podman is a drop-in Docker replacement and that it doesn't require the daemon. That's probably selling it short. There'll be a lot more that it can do. What are some of those extra features above and beyond the Docker functionality?

BRENT BAUDE: The first thing is the pods. Of course, that's where the name comes from. We are able to support pods in Podman. They differ slightly in actual implementation from a pod that you might know in Kubernetes, but the behavior is essentially the same.

We also have this notion where we think the long term future of Podman is. We have this notion of being able to run some containers or pods locally and then capture them in Kubernetes format, like a YAML file format. That's called generate. And then we have a play command that allows you to replay that with Podman.

However, you can also take that same YAML file and throw it at Kubernetes, and you've got the same thing running there. That interaction is what I meant by, this is where we think our future and growth can be and continue to make that easier for people, continue to make that a seamless transition, perhaps even a basic component of what developing a services container, for example, might look like.

Try it out locally. Lift it to Kubernetes. It works fine. Or if you need to debug something that's failing in Kubernetes, you could theoretically bring that down on your single node with Podman, run it, and get a good idea of what's going on.

DANIEL WALSH: That's one key feature, that Podman basically can run pods and really sort of bridge the world between sort of running local containers and then running containers and sort of redistribute a Kubernetes environment. So we wanted to make it easy to go from a simple Podman command line to this more complex Kubernetes YAML file just by saying, oh, I created these containers. They're running great. Now, I do podman-generate-kube and out pops the Kubernetes YAML file that's ready to take the containers I just built and run them in a Kubernetes world.

Me coming from a security point of view, I think the big advantages of Podman over Docker show themself with probably Portman's most popular thing, which is that it runs as rootless mode. Because we're doing the simplified creating of child process underneath it, Podman was able to easily plug into the use of namespace world. And suddenly we're able to do almost everything you can do as a rootfull Docker or a Podman, you can do it in rootless mode. And that's really what sort of shot off the popularity of Podman.

Now, Docker eventually responded, and you can sort of run Docker as a rootless mode. But some of the Docker design becomes a little bit silly when you run it in rootless mode. If you log into your system, and suddenly you have to run two different daemons inside of your home directory in order to run Docker. And what I've always felt about Docker is this doesn't make sense if I just want to run a simple container on my home directory, that I have to run all this infrastructure in order to run the container, whereas I can just run the Podman command.

Another problem with Docker versus Podman for running rootless mode is, again, if I just want to run a container, I want to run a container. I don't want to have to run the container tools all the time. So in Podman, I could just say podman-run, and Podman pulls down the container image, starts it up, and then disappears, right? So there's no overhead. There's nothing going on, unless I want to interact with the container. But if I just want the containerized application.

A couple of other things, I mentioned that Docker was openly hostile to systemd. Well, Podman has sort of embraced systemd. So it's simple to run a systemd inside of your container. But we also — similar to what Brent talked about for podman-generate-kube — we also have podman-generate-systemd. So we can easily create systemd unit files based off your container images.

So if you build an application and you just want to run it on a service, the way you start most services is systemd unit files. So podman-generate-systemd will take your Kubernetes and pods and generate systemd unit files. Then you can just distribute the systemd unit files, and people who just start up a service, and it's running a containerized service.

Further, we've developed on that is the idea of making those services auto-update. So imagine you're doing an IoT device or an edge server — these little tiny applications sitting out there on top of the container images. You really want that machine to just boot up, start up your service, maybe running a Podman command inside of a unit file. It goes out to a service, pulls down an application, and starts running it. That image eventually gets updated because of a security fix or something like that. You don't want to have to go out to that machine, or thousands of machines, and tell them to update.

So inside of Podman tooling right now, we have the ability to have it auto-update, which means that basically this is a job that runs, say, in a systemd timer, and periodically goes back out to a service and says, is there a new image there? And if the new image is available, it basically tells Podman to pull down the new image and recreates the container off of the new image.

So now you can get basically human free machines sitting out there running as edge servers, all because the integration between Podman and systemd is available. Those are the types of features that we're looking to add. One of the key things when we were doing Podman originally was we were doing Podman command line interface.

Originally, we used to say replace Docker with Podman, we would say just alias Docker with Podman and you're done. Well, that was true if you use the command line. But the secondary need that we faced very quickly was the need to interact with all these services, all these remote APIs that we're communicating with the Docker socket.

So Brent has led the effort to basically provide the Podman sock, so I'll let Brent talk a little bit about what we've done in that area.

BRENT BAUDE: It became very evident to us that our users really, really needed something that simulated a Docker socket. And that was not our traditional approach with Podman primarily being CLI, as Dan mentioned at this time. What we ended up doing is creating a similar RESTful service for Podman that serves up really two layers of service, if you will.

One is a pure Podman set of APIs that were implemented. It also offers a layer of services that mimic Docker’s API. What people were really after is, suppose they had written an application that used docker-py, the Python library, for communicating with Docker, they wanted to be able to redirect that towards a socket that was controlled by Podman and be able to run their application without having to port.

That quickly became, I want to run Docker Compose, I want to run these Java test utilities, I want to run the Docker CLI on top of Podman. It went sort of nuts. That's always something that Dan and I find interesting is we may release some sort of feature or whatever and our users just run with it. And they run it to exhaustion in terms of this is what I wanted to do with it. And we sit there and marvel at, we've never even thought about these kinds of things. We were just trying to plug a hole or offer a new feature.
-->

CRAIG BOX: 多くの人が、PodmanはDockerの代替品であり、デーモンを必要としないという考えを頭の片隅に持ってこのエピソードに臨むでしょう。しかし、それは短絡的な考えかもしれません。まだまだできることはたくさんあるはずです。Dockerの機能を超えた追加機能にはどのようなものがありますか？

BRENT BAUDE: まず第一に「Pod」です。もちろん、それが名前の由来です。PodmanではPodに対応しています。KubernetesでおなじみのPodとは、実際の実装が若干異なりますが、動作は基本的に同じです。

また、私たちはPodmanの長期的な未来をどう考えるかという概念を持っています。いくつかのコンテナやPodをローカルで実行し、それらをKubernetesフォーマット（YAMLファイルフォーマットなど）で取り込むことができるという概念があります。これを「generate」と呼びます。そして、それをPodmanで再生するための「play」コマンドがあります。

しかも、同じYAMLファイルをKubernetesに投げても、同じものがそこで実行されます。このインタラクションこそが、私たちの未来と成長を意味しており、人々にとってより簡単で、シームレスな移行を可能にし、おそらくサービスコンテナを開発するための基本的なコンポーネントになると思います。

ローカルで試してみる。それをKubernetesに上げてみてください。問題なく動作します。あるいは、Kubernetesで失敗したものをデバッグする必要がある場合、理論的にはPodmanを使って手元のシングルノードに持ってきて、それを実行し、何が起こっているかをよく知ることができます。

DANIEL WALSH: これは重要な機能の1つです。Podmanは基本的にPodを実行することができますが、ローカル環境でのコンテナの実行とKubernetes環境での再実行との橋渡しをすることができるのです。そう、シンプルなPodmanのコマンドラインから、複雑なKubernetesのYAMLファイルまで、簡単に移行できるようにしたかったです、こんな感じで。「よし、このコンテナを作った、順調に動いてる。それじゃ、podman-generate-kubeを実行してみて。さっき作ったコンテナを、Kubernetesの世界でビルドするだけで実行できる準備が整ったYAMLファイルが出来上がるよ。」

セキュリティの観点から考えると、PodmanがDockerよりも優れている点は、おそらくPodmanで最も人気のあるルートレスモードで動作することだと思います。Podmanは、その下で子プロセスの作成を簡略化しているため、名前空間の使用に簡単に接続することができます。。そして突然、ルートフルなDockerやPodmanでできることのほとんどすべてを、ルートレスモードでできるようになったのです。これがPodmanの人気を高めるきっかけになりました。

さて、Dockerもこれに対応し、Dockerをルートレスモードで動かすことができるようになりました。しかし、ルートレスモードで実行すると、Dockerの設計の一部が少し馬鹿げたものになります。システムにログインすると、突然、Dockerを実行するために、ホームディレクトリ内で2つの異なるデーモンを実行しなければならなくなります。私がDockerについて常に感じていることは、ホームディレクトリでシンプルなコンテナを実行したいだけなのに、コンテナを実行するためにすべてのインフラを実行しなければならないというのは意味がない、ということです。

ルートレスモードでのDockerとPodmanのもう一つの問題は、コンテナを実行したいだけなら、コンテナを実行するだけでいいということです。常にコンテナツールを起動しておく必要はありません。Podmanでは、podman-runと言うだけで、Podmanがコンテナイメージを引き出し、起動して、消えてしまいますよね？つまり、オーバーヘッドがないのです。私がコンテナを操作したいと思わない限り、何も起こりません。でも、単にコンテナ化されたアプリケーションが欲しいだけならば、これで十分なのです。

他にもいくつかありますが、Dockerはsystemdを公然と敵視していると言いました。Podmanにはちょっとしたsystemdを動かすための機能があります。ですから、コンテナの中でsystemdを実行するのは簡単です。また、Brentが話したpodman-generate-kubeコマンドと同様に、podman-generate-systemdコマンドも用意しています。これを使うとコンテナイメージから簡単にsystemdのユニットファイルを作成することができます。

つまり、アプリケーションを作って、それをサービスで動かしたい場合、ほとんどのサービスを起動する方法はsystemdユニットファイルです。podman-generate-systemdコマンドは、KubernetesとPodを使ってsystemdユニットファイルを生成します。そうすれば、systemdユニットファイルを配布して、サービスを起動するだけで、コンテナ化されたサービスが実行されるようになります。

さらに、サービスを自動更新するというアイデアも開発しました。例えば、IoTデバイスやエッジサーバーなど、小さな小さなアプリケーションがコンテナイメージの上に置かれているような場合です。このような小さなアプリケーションは、コンテナイメージの上に置かれています。マシンを起動してサービスを立ち上げ、ユニットファイルの中でPodmanコマンドを実行することができます。サービスにアクセスして、アプリケーションを引き出し、実行します。そのイメージは、最終的にはセキュリティ修正などの理由でアップデートされます。その際に、そのマシンや何千台ものマシンにアップデートを指示しに行くのは面倒でよね。

つまり、基本的にはsystemdタイマーで実行されるジョブで、定期的にサービスに戻って「新しいイメージがありますか」と尋ねます。そして、新しいイメージが利用可能であれば、基本的にはPodmanに新しいイメージを引き出すように指示し、新しいイメージからコンテナを再作成します。

Podmanとsystemdが統合されたことで、基本的に人間に依存しないマシンをエッジサーバーとして稼働させることができるようになったわけです。Podmanとsystemdの統合により、これらの機能を追加していきたいと考えています。もともとPodmanを開発していたときに重要だったのは、Podmanのコマンドラインインターフェイスを開発していたことです。

もともとは、DockerをPodmanに置き換えようと言っていたのですが、それは「Docker」コマンドを「Podman」コマンドでエイリアス化するだけで完了してしまいます。それはコマンドラインを使う場合には正しいことです。しかし、すぐに直面した副次的なニーズは、Dockerソケットで通信しているすべてのサービス、すべてのリモートAPIと対話する必要があることでした。

そこで、Brentが中心となって、基本的にPodmanソケットを提供することにしたので、Brentにそのあたりのことを少し話してもらおうと思います。

BRENT BAUDE: 我々のユーザーは、Dockerソケットをシミュレートするものを本当に必要としていることが非常に明らかになりました。そしてそれは、今回Danが述べたように、Podmanが主にCLIであるという我々の伝統的なアプローチではありませんでした。結局、私たちが行ったのは、Podmanのために同様のRESTfulなサービスを作り、言ってみれば2層のサービスを提供することでした。

一つは、実装された純粋なPodmanのAPIセット。またもう一つは、DockerのAPIを模倣したサービスの層を提供しています。人々が本当に求めていたのは、Pythonライブラリのdocker-pyを使ってDockerと通信するアプリケーションを書いていたとしたら、それをPodmanが制御するソケットにリダイレクトして、移植することなくアプリケーションを実行できるようにしたいということでした。

それはすぐに、Docker Composeを実行したい、Javaのテストユーティリティを実行したい、Podmanの上でDocker CLIを実行したい、となりました。それはちょっとした狂気の沙汰でした。これは、Danと私がいつも面白いと思っていることですが、私たちがある種の機能や何かをリリースしても、ユーザーはそれを使いこなしてしまうのです。ユーザーは、「これを使って何がしたいか」という観点から、とことん使い倒すのです。そして、私たちはそこに座って、こういうことを考えたこともなかったと感嘆するのです。私たちは、ただ穴を塞いだり、新しい機能を提供しようとしていただけなのです。

---
## (28:59) Docker socket and Docker Compose
<!--
CRAIG BOX: Well, it sounds a little bit like what Dan was saying before about the distinction between the web-based approach or the monolithic API service approach from Docker the company, versus Red Hat people being operating system people. It almost feels like you were having to implement things that you don't necessarily agree with or you wouldn't necessarily advise people to use.

BRENT BAUDE: It does complicate matters because you now have a socket even though we use socket activated services with systemd. So it's not technically running all the time, but you do have these extra doors that can be abused if the circumstances are dead right.

So that really wasn't our model. It really wasn't our approach. But it became very clear that in order to be successful, that was something being heavily, heavily used by users, customers, companies, however you want to say. And we would have to be able to do the same thing.

And in fact, it translates about everywhere. The same thing would be true for interacting with GitLab, who's got services that run on localized runtimes and they use the APIs to communicate back and forth. In order for Podman to be able to be used in the same manner, we were going to have to offer similar API, such that we don't require everyone to have to redo a whole bunch of work.

DANIEL WALSH: In a perfect world, we'd prefer everybody to write their multi-service applications in Kubernetes YAML, but Docker Compose has this huge install base, and applications have been built based on Docker Compose. Basically, we were forced to allow these people to actually work.

Matter of fact, there were projects in the open source world like Podman Compose, which was attempting to do what Docker Compose does but with the Podman command line, but was falling somewhat short of what people expected. We really had to have basically a socket that could implement what the docker.sock did.

Interesting thing that we did do, though, in addition to that, and it was always like embrace and then extend it to add additional features, we actually added a way to run Docker Compose rootless. So we actually provide the Docker socket in rootless mode so that you can run your entire Compose scripts without having to be root on the system.

So again, using the really core underlying technologies, we're able to enhance and add more security to what you were traditionally able to do with just a Docker daemon.

BRENT BAUDE: I would like to just clarify when Dan says that we believe that play-kube and YAML files are better over Docker Compose, for example. What we're getting at is that file is multipurpose. You can run it in Podman. You can run it in Kubernetes.

You create a Docker Compose file, and it only works with Docker. Had we not implemented those common APIs with Docker, it wouldn't work with Podman either. That's why he's sort of indicating that this seems like a better solution because it has more uses and more commonality with the rest of the world.
-->

CRAIG BOX: 先ほどダンが言っていた、Docker社のWebベースのアプローチやモノリシックなAPIサービスのアプローチと、Red Hatの人々がOSの専門家であることの違いに少し似ていますね。自分が必ずしも賛成できないことや、人に使うことを必ずしも勧められないことを実装しなければならなかったように感じます。

BRENT BAUDE: 問題を複雑にしているのは、systemdでソケット起動型サービスを使用しているにもかかわらず、今はソケット自体があることです。技術的には常時稼働しているわけではありませんが、状況が悪ければ悪用される可能性のある余分なバックドアを持っていることになります。

ですから、これは私たちのモデルでも、私たちのアプローチでもありませんでした。しかし、成功するためには、ユーザーや顧客、企業などに大いに利用されるものでなければならないことは明らかでした。私たちも同じことができるようにならなければなりません。

そして実際に、それはあらゆる場所に当てはまります。同じことが、GitLabとのやりとりにも当てはまります。GitLabは、ローカライズされたランタイム上で動作するサービスを持っており、APIを使ってやりとりをしています。Podmanを同じように使えるようにするためには、同じようなAPIを提供しなければなりませんでした。

DANIEL WALSH: 完璧な世界では、私たちは誰もがマルチサービスアプリケーションをKubernetesのYAMLで書くことを望んでいますが、Docker Composeにはすでに巨大なインストールベースがあり、多くのアプリケーションはDocker Composeに基づいて構築されています。基本的にはこれらの人々が望む動作を認めざるを得ませんでした。

実際のところ、オープンソースの世界にはPodman Composeのようなプロジェクトがあり、Docker ComposeがやっていることをPodmanのコマンドラインで実現しようとしていましたが、人々が期待していたものよりも多少不足していました。私たちは、Dockerソケットが行うことを実装できる基本的なソケットを用意する必要がありました。

面白いことに、それに加えて、私たちはDocker Composeをルートレスで実行する方法を追加しました。つまり、ルートレスモードのDockerソケットを提供し、システムのrootになることなくComposeスクリプト全体を実行できるようにしました。

このように、基本となる技術を利用して、従来のDockerデーモンだけで実現していたことを強化し、セキュリティを強化することができます。

BRENT BAUDE: Danが、例えばDocker Composeよりもpodman-play-kubeコマンドやYAMLファイルの方が優れていると信じていると言っていることを明確にしておきたいと思います。私たちが言いたいのは、あのファイルは多目的に使えるということです。Podmanでも実行できます。Kubernetesでも実行できます。

Docker Composeのファイルを作ると、Dockerでしか使えません。もしDockerで共通のAPIを実装していなかったら、Podmanでも動かなかったでしょう。だからこそDanは、より多くの用途と世界との共通性を持つこちらの方が、より良い解決策のように思えるということを示しているのだと思います。

---
## (32:02) Podman in Podman
<!--
CRAIG BOX: You mentioned before people wanting to run Docker in Podman. There's obviously a world of opportunity opened up when people were able to run Kubernetes inside Docker. Does it make sense to run Podman in Podman?

DANIEL WALSH: I just wrote two major blogs on that because that's a very common question we get. It does. Most people do it for CI/CD systems. So a lot of people wanted to test their containers and build their containers inside of the CI/CD system, and usually they want to run Podman inside of Docker, or they want to run Podman inside of Kubernetes, or they want to run Podman inside of Podman.

There's also Buildah, which is the underlying technology that we use for podman-build, is similarly used. The reason I wanted to write the blog is Podman opens up this incredible amount of variety of ways that you might want to run Podman inside of a container. The blogs are available at redhat.com/sysadmin or just google Podman inside of a container.

CRAIG BOX: Or read the show notes.

DANIEL WALSH: Or the show notes, yes. It's a very common request to be able to run containers within containers and therefore Podman inside of containers. And people are doing like Kubernetes inside of Podman now. Everything gets turned on itself, and I'm sure somebody somewhere is probably running a Docker daemon inside of a Podman container as well. So every possible combination.
-->

CRAIG BOX: 以前、Podmanの中でDockerを動かしたいという話がありましたね。人々がDockerの中でKubernetesを実行できるようになったことで、明らかにチャンスのある世界が開けました。Podmanの中でPodmanを動かすことに意味はあるのでしょうか？

DANIEL WALSH: これは非常によくある質問なので、2つの大きなブログを書きました。意味はあります。ほとんどの人はCI/CDシステムのためにそれを行っています。多くの人がCI/CDシステムの中でコンテナをテストしたり、コンテナを構築したりしたいと考えており、通常はDockerの中でPodmanを実行したり、Kubernetesの中でPodmanを実行したり、Podmanの中でPodmanを実行したりしています。

また、Podman-buildの基盤技術であるBuildahも同様に使われています。私がこのブログを書こうと思った理由は、Podmanによって、コンテナ内でPodmanを実行する方法が驚くほど多様になるからです。ブログは redhat.com/sysadmin で見ることができますし、「Podman inside of a container」のキーワードでググってもいいでしょう。

CRAIG BOX: または、ショーノートをお読みください。

DANIEL WALSH: ショーノートもね。コンテナの中でコンテナを走らせたいというのは非常によくある要望で、そのためにPodmanをコンテナの中で走らせています。Podmanの中にKubernetesを入れるようなことも行われています。また、どこかで誰かがPodmanコンテナの中でDockerデーモンを動かしているのではないでしょうか。あらゆる組み合わせが可能なのです。

---
<!-- 33:19 Buildah pronunciation -->
<!--
CRAIG BOX: And in case anyone out there is only listening and isn't reading along with the show notes, we should point out that Buildah is actually spelled B-U-I-L-D-A-H, and it's not just the way Dan says it. Although I'm sure that was why it was named that way.

DANIEL WALSH: It was to make fun of me speaking correctly with a Boston accent.
-->

CRAIG BOX: また、番組ノートを読まずに聞いている人のために言っておくと、Buildahは実際にB-U-I-L-D-A-Hというスペルで、Danの言い方だけではありません。だからこそこのような名前になったと思いますが。

DANIEL WALSH: ええ、私がボストン訛りで正しく話すことを揶揄したものです。

---
## (33:34) Container Runtime and CRI-O
<!--
CRAIG BOX: Kubernetes used to use Docker as its container engine. It has now moved to containerd and slightly more appropriate leveled runtimes. Does it make sense to use Podman as the runtime for Kubernetes, or is that the job for the lower level runtime under it, like CRI-O?

DANIEL WALSH: The two major tools for running containers in Kubernetes now is containerd and CRI-O. When we developed CRI-O, which actually predates the creation of containerd, CRI-O was really using the same underlying technology as Podman that containers/storage and containers/image.

Our goal with CRI-O was to really dedicate and lock down the container engine that Kubernetes would use. During the years when OpenShift was first being developed on top of Kubernetes, every time Docker would update, Kubernetes would be blown away by all the changes. It was incredibly unstable. Not anything Docker was doing incorrectly, it was just Docker was updating its APIs and things like that. And Kubernetes was having a hard time keeping up.

We had problems with people using a version of Kubernetes that didn't match up to the version that we tested with Docker. When we developed CRI-O, we wanted to basically dedicate — if you're running Kubernetes 1.10, you run CRI-O 1.10; Kubernetes 1.11, CRI-O 1.11.

And then we would test them together in a single unit. CRI-O really is taking our underlying technologies and just looking at the specific version of Kubernetes and then optimizing everything in the world to run with it. Docker created a competitor to it called containerd, which does pretty much similar things to what CRI-O does.

In my opinion, containerd is a more closed in monolithic environment, where CRI-O was built on top of underlying libraries that are fully open sourced, as opposed to being one entity.
-->

CRAIG BOX: Kubernetesは、コンテナエンジンとしてDockerを使用していました。今ではcontainerdや、もう少し適切なレベルのランタイムに移行しています。KubernetesのランタイムとしてPodmanを使うことに意味があるのか、それともそれはCRI-Oのようなその下の下位レベルのランタイムの仕事なのでしょうか？

DANIEL WALSH: 現在、Kubernetesでコンテナを実行するための2大ツールは、containerdとCRI-Oです。私たちがCRI-Oを開発したとき、実はcontainerdの誕生よりも前のことですが、CRI-OはPodmanと同じように、コンテナストレージとコンテナイメージという基礎技術を使っていました。

CRI-Oの目的は、Kubernetesが使用するコンテナエンジンを他の目的から専用化し、ロックダウンすることでした。OpenShiftがKubernetesに基づいて開発されていた当初、Dockerがアップデートするたびに、Kubernetesはすべての変更によって影響を受けていました。信じられないほど不安定だったのです。Dockerが間違ったことをしていたわけではなく、DockerがAPIを更新したりしていただけなのですが。Kubernetesはそれについていくのが大変でした。

我々がDockerでテストしたバージョンと一致しないKubernetesのバージョンを使っている人がいるという問題がありました。CRI-Oを開発するにあたり、基本的にはKubernetes 1.10を使っている人はCRI-O 1.10を、Kubernetes 1.11を使っている人はCRI-O 1.11を使うようにしたいと考えました。

そして、それらを1つのユニットとしてまとめてテストします。CRI-Oは、私たちの基本的な技術をベースに、Kubernetesの特定のバージョンだけを見て、それに合わせて世界のあらゆるものを最適化しています。Docker社はcontainerdという競合製品を開発しましたが、これはCRI-Oとほぼ同様のことをします。

私の考えでは、containerdはより閉鎖的なモノリシックデザインで、CRI-Oは完全にオープンソース化された基礎的なライブラリの上に構築されており、モノリシックデザインではありません。

---
## (35:25) containerd and CRI-O
<!--
CRAIG BOX: I know what the relative amount of usage of both of those platforms is, but I think a lot of people might think of CRI-O as being the runtime under OpenShift, and most of the other implementations tend to use containerd, as I understand it at least. Is there a point where you're sort of swimming against the tide and maintaining your own thing, and you would be looking to merge those things? Or do you think that having multiple implementations of the same idea, like you've said before, is useful in helping make sure you don't have a monoculture around one implementation?

DANIEL WALSH: I always find it funny that in a world where we have 10 different web browsers and 40 different ways of editing files and lots and lots of different ways to doing almost everything in the world, everybody wants to have a single way of running a low level container engine that is working underneath Kubernetes. I don't see us swimming against the tide because this is more of a group of three or four engineers working on something that we can make work perfectly with the way OpenShift and Kubernetes wants to work.

First of all, CRI-O was used in other worlds besides OpenShift, but even if it's just dedicated to OpenShift, if we can get the best OpenShift experience out of CRI-O, why would we necessarily want to work on containerd? Lastly, the underlying engines aren't changing all that drastically.

These things aren't rapidly evolving. They're changing slowly over time, and that's goodness, right? That means that there's a stabilization going on. So there's not really a huge amount of differences between containerd and CRI-O, but they do work very independently.

One of the things that CRI-O can do that containerd can't do is take advantage of enhancements that we put in for things like Podman. So some of the ways that we do user namespace inside of Podman got evolved into CRI-O, and some of the advanced features, events, some of the image signing, things like that. To us, moving to containerd doesn't make much sense at all.
-->

CRAIG BOX: CRI-OはOpenShiftのランタイムで、その他の実装はcontainerdを使用していると考えている人が多いのではないでしょうか。流れに逆らって泳ぎ、独自のものを維持しているような状況で、それらを統合しようとするポイントはありますか？それとも、以前にもおっしゃっていたように、同じアイデアの実装を複数持つことは、1つの実装の周りにモノカルチャーができないようにするために有効だとお考えですか？

DANIEL WALSH: 10種類のWebブラウザや40種類のファイル編集方法など、世の中にはほとんどすべてのことを行うためのさまざまな方法があるのに、誰もがKubernetesの下で動作する低レベルのコンテナエンジンを実行する単一の方法を求めているのは、いつもおかしいと思っています。私たちは、OpenShiftやKubernetesが望んでいる方法で完璧に動作させることができるものを、3～4人のエンジニアのグループで開発しているので、流れに逆らうことはないと思います。

まず、CRI-OはOpenShift以外の世界でも使用されていましたが、たとえOpenShiftに特化したものであっても、CRI-Oから最高のOpenShift体験が得られるのであれば、必ずしもcontainerdに取り組みたいとは思わないのではないでしょうか。最後に、基盤となるエンジンはそれほど劇的には変化しません。

これらは急速に進化しているわけではありません。時間をかけてゆっくりと変化していく、それがいいんですよね。つまり、安定化が進んでいるということです。ですから、containerdとCRI-Oの間には大きな違いはありませんが、非常に独立して機能しています。

CRI-Oにできてcontainerdにできないことの一つは、Podmanなどのために私たちが入れた拡張機能を利用することです。Podman 内でユーザーネームスペースを利用する方法のいくつかは CRI-O に進化しましたし、高度な機能、イベント、イメージ署名などのいくつかもそうです。私たちにとって、containerdに移行することはあまり意味がありません。

---
## (37:16) Desktop experience for Podman
<!--
CRAIG BOX: Within the last few weeks, let's just say there has all of a sudden been a desire to look at good desktop experience for running container environments. Coincidentally with any announcements that may or may not have happened, there has been support announced for Podman on Windows and Mac. Were those things completely coincidental?

BRENT BAUDE: Yes.

CRAIG BOX: That's good luck on your part then, I should say.

BRENT BAUDE: And a little bit of no.

CRAIG BOX: I take it back.

BRENT BAUDE: I like to tell an old IBM adage about development in a non-open source world that IBM used to be. There was an old adage that IBM doesn't release products. They escape. We had a little bit of that this time. The support was actually there and had been upstream. I believe it had been released one version earlier, in 3.2, but it was a limited working version.

DANIEL WALSH: We're talking about Podman on a Mac?

BRENT BAUDE: Yeah, and it didn't have the network proxying and a few other things. We were working on it anyways for 3.3, and 3.3 happened to go out. That said, it was a little bit of forcing our hand. We weren't quite ready to totally go for it, even though that feature had already been in Podman and people were using it, even prior to 3.3, using it primarily to build images, which is something it did well at that point.

So it was a little bit of both, but it was largely coincidental in that case. Now, on the flip side, we've seen a flood of interest and users flooding to report bugs, which there are about it. It's been remarkable, unabashedly remarkable.

DANIEL WALSH: Let's go into a little bit of the history of Podman on Mac. First of all, a lot of people think that when you're running a container on a Mac, you're running it sort of natively. That's another misconception. Containers are really a Linux thing. So when people talk about containers, they're running them inside of a Linux operating system. There are Windows containers, but we'll push them off as being this tiny subset. When people talk about most containers, they're talking about Linux operating systems.

So whether you're using Podman or Docker on a Mac, you're talking to a service that's running inside of a VM. To run a container, Podman — actually Brent did most of the work on this — we wanted to create an experience with someone who would just install Podman on a Mac, then run Podman and it would notice that you don't have a VM and would basically pull in a virtualized operating system that you could then, Podman on the Mac could talk to to launch your containers.

Brent built what we call podman-machine. Simply, if you just install Podman and you do podman-machine-init, it will go out and grab — really, Fedora CoreOS — and pull it in onto the machine. It will do all the configuration to run the VM. Then if you do podman-machine-start, it will actually start up the Fedora CoreOS, and then the Podman commands would be pre-wired then to talk to Podman inside of the VM.

This is very similar to what Docker machine is doing underneath the covers. As Brent said, when the announcements happened over the last couple of weeks, we had just basically completed the technology to make it work. And we've been fairly happy that we weren't even ready to really announce it and start pushing it when all of a sudden, we get this flood of interest in it.

Now, we're having meetings to talk about how could we add the graphics tools, the graphic capabilities, because a lot of people want to do things like have a little Podman icon in their desktop and click on it and say I want to stop the machine or start the machine or refresh the machine. And a lot of that — what I call chrome — doesn't exist yet. So that's the thing we're working with the open source community to add to the Mac.

The hard part for us really is not the Podman parts of this. The hard part is figuring out, how do we deal with operating systems, right? How do we deal with these virtual machines? And even when we're looking at Windows, we have the same type of issues.

So when we look at WSL2, people say they want to run Podman, but Podman is available on 10 different distributions. We want to end up with people having the best Podman experience, so we really want the upstream Podman project to recommend which distribution you're going to run inside the WSL2 to get the latest version of Podman and things like that. So we're working out the logistics of ‘how do we do that?’

Right now for Mac, we're doing it with Fedora CoreOS underneath the covers, just because we work well with the Fedora community and can sort of guarantee that they have the latest version of Podman on the service so it matches up to the Podman client that's running on the Mac.

BRENT BAUDE: Apple Silicon support is coming. We didn't take the same approach that Apple did, in the sense that they ship their own QEMU. So we've been waiting for these enablement patches for Apple Silicon to get into QEMU proper, as it would be typical of Linux and open source and Red Hat to do.

We've actually been granted a workaround from Brew to go ahead and put them in now, so that should be very, very soon, and in fact, maybe even in 3.4, which is being created as we speak.
-->

CRAIG BOX: ここ数週間の間に、コンテナ環境を実行するための優れたデスクトップ環境を見てみたいという願望が突然出てきたと言っていいでしょう。偶然にも、何かの発表と同時に、WindowsとMacでのPodmanのサポートが発表されました。それらはまったくの偶然だったのでしょうか？

BRENT BAUDE: そうです。

CRAIG BOX: それはあなたの幸運というべきでしょう。

BRENT BAUDE: そして、少しだけ「いいえ」です。

CRAIG BOX: 前言撤回です。

BRENT BAUDE: 私は、かつてのIBMの、オープンソースではない世界での開発に関する古い格言を語るのが好きです。「IBMは製品をリリースしない」という古い格言があります。彼らは逃げるのです。今回はそれが少しだけありました。デスクトップ機能のサポートは実際にあったし、アップストリームにもありました。それは1つ前のバージョン、v3.2でリリースされていたと思いますが、それは限定的なワーキングバージョンでした。

DANIEL WALSH: MacのPodmanの話ですか？

BRENT BAUDE: ええ、ネットワークプロキシ機能やその他いくつかの機能がありませんでした。とにかくv3.3に向けて作っていたのですが、たまたまv3.3がリリースされました。とはいえ、これはちょっと無理矢理だったんですよ。その機能はすでにPodmanに搭載されていて、v3.3以前から人々は主にイメージを構築するために使用していました。

つまり、偶然でもあり必然でもあったのですが、今回の場合はほとんど偶然でした。その一方で、ユーザーの関心は高く、バグの報告が殺到しています。これは驚くべきことで、臆することなく驚くべきことです。

DANIEL WALSH: では、Mac版Podmanの歴史を少し紹介しましょう。まず、多くの人が、Macでコンテナを実行すると、ある種ネイティブに実行されていると思っています。これも誤解です。コンテナは本来、Linuxのものです。つまり、コンテナといえば、Linux OSの中で実行されるものなのです。Windows用のコンテナもありますが、それはごく一部のものだと考えています。コンテナといえば、ほとんどがLinux OSのことを指しています。

つまり、MacでPodmanやDockerを使っていても、それはVMの中で動作しているサービスを指しています。私たちは、MacにPodmanをインストールしてPodmanを実行すると、VMがないことがわかると、仮想化されたOSを取り込み、MacのPodmanがコンテナを起動するように対話を行う、という体験を作りたかったのです。

Brentは、「Podman-machine」と呼ばれるものを作りました。Podmanをインストールして「podman-machine-init」コマンドを実行するだけで、Fedora CoreOSをローカルマシンにダウンロードします。そしてVMを動かすためのすべての設定を行います。「podman-machine-start」コマンドを実行すると、Fedora CoreOSが実際に起動し、PodmanコマンドがVM内のPodmanと通信するように事前に設定されます。

これは、Docker Machineが水面下で行っていることとよく似ています。Brentが言ったように、ここ数週間の間に発表されたとき、私たちはそれを動作させるための技術を基本的に完成させたところでした。そして、私たちはそこそこ満足しています、なぜなら実際に発表してプッシュし始める準備さえできていなかったのに、突然、これほどまでに関心が殺到したのですから。

というのも、多くの人がデスクトップに小さなPodmanのアイコンを置いて、それをクリックして「マシンを停止したい」「マシンを起動したい」「マシンを更新したい」などの操作をしたいと考えているからです。私がクロームと呼んでいるそのような機能はまだ存在していません。だからこそ、私たちはオープンソースコミュニティと協力してMacに追加しようとしているのです。

私たちにとって難しいのは、実際にはPodmanの部分ではありません。難しいのは、OSをどう扱うかということです。仮想マシンをどのように扱うか。Windowsを見ていても、同じような問題があります。

WSL2において「Podmanを使いたい」と言われますが、Podmanは異なる10種類のディストリビューションで提供されています。そこで、アップストリームのPodmanプロジェクトには、最新バージョンのPodmanを入手するためにWSL2内でどのディストリビューションを実行するかを推奨してもらいたいと考えています。そのためにはどうすればいいのかということを考えています。

現在Mac向けには、Fedora CoreOSがこれに相当しますが、これは、Fedoraコミュニティとの関係が良好で、Mac上で実行されるPodmanクライアントとマッチするようにVM内のサービス上のPodmanが最新バージョンであることが保証できるからです。

BRENT BAUDE: Apple Siliconのサポートも予定されています。私たちは、Appleが独自のQEMUを出荷したようなアプローチをとりませんでした。そのため、Linuxやオープンソース、Red Hatのように、Apple SiliconがQEMUに適切に対応するための有効化パッチを待っていました。

実際、Brewからワークアラウンド（回避策）を得て、今すぐにでも導入できるようになっていますので、実際には、現在作成中のv3.4にも搭載されるかもしれません。

---
## (42:28) Remote client for Mac
<!--
CRAIG BOX: With the way that you've described the different models between Docker and its socket and Podman running processes locally, as the user who's executing the command, it would make sense that the Docker approach worked well when you just have the client on the Mac and the service, if you will, running on the VM. What changes did you have to make to Podman to be able to support that disconnection?

BRENT BAUDE: Very little, in fact. It wasn't well known, except to the team, that Podman has what we refer to as a remote client, and there's actually a binary called the remote. The main Podman binary and the regular one that most people use also has the ability to be a remote client and just connect to a Linux VM anywhere, or I should say securely through our system connection management in Podman.

That whole conversation of, I'm sitting on a Mac — in fact, that's the way you ran it on a Mac before was you ran the Podman remote binary, and that connected you to a Linux VM somewhere — but the difference was then, you had to create that Linux VM or you had to create that Linux server yourself.

CRAIG BOX: Whereas now it's just podman init, for example, and it will make that happen for you?

BRENT BAUDE: Correct. Dan talked about having a better user experience. Some of it, much like the API conversation, was something we had been talking about. Do we really want to go there and now start dealing with VMs and the fact that there isn't a consistent VM technology across all these platforms we're trying to support? Again, it became evident by user feedback, yes, you do want to go do that. And so we had to take the dive.

DANIEL WALSH: One of the things that we did when we were developing the socket is we wanted to make sure that all of the Podman commands would work against the socket as well as locally. So our entire development tool chain in the CI/CD system is always checking Podman and what we call Podman remote against the socket to make sure that the interfaces work exactly the same.

We always had Podman remote. As Brent said, there is a podman dash remote to allow you to take this sort of standard Linux Podman and talk to remote machines. Now, I would like to point out that on a Mac, it is Podman remote talking to a Podman inside of a container.

In Windows and WSL2, Microsoft has done some really cool stuff. It is running the real Podman inside of, say, the Windows shell, the PowerShell. So it's not really using Podman remote when you're running with the WSL version of Podman. I'm not sure how they do it, but it actually works. We've been told by lots and lots of our users, they just love the way that Podman works on WSL2.
-->

CRAIG BOX: Dockerとそのソケット、そしてPodmanがコマンドを実行するユーザーとしてローカルにプロセスを実行するという異なるモデルについて説明していただきましたが、Mac上にクライアントがあり、VM上でサービスが実行されている場合には、Dockerのアプローチがうまく機能することは納得できます。その接続性をサポートするために、Podmanにどのような変更を加えなければならなかったのでしょうか？

BRENT BAUDE: 実は、ほとんどありません。Podmanには、我々がリモートクライアントと呼ぶものがあり、実際にリモートと呼ばれるバイナリがあることは、チーム内以外ではあまり知られていませんでした。ほとんどの人が使っているメインのPodmanバイナリは、リモートクライアントとして、どこにいてもLinux VMに接続できる機能を持っていますが、Podmanのシステム接続管理機能を使って安全に接続することもできます。

概要をお伝えすると、MacでPodmanを使うというのは、実は以前はPodmanのリモートバイナリを実行して、どこかのLinux VMに接続する必要がありました。当時との違いになりますが、その時はLinux VMを作成するか、自分でLinuxサーバーを用意しなければならなかったのです。

CRAIG BOX: 今は、例えば「podman init」を実行するだけで、それを実現してくれるのですね。

BRENT BAUDE: そうです。Danは、ユーザーエクスペリエンスの向上について述べました。その中には、API議論のような、私たちが話したことが含まれます。今度はVMや、サポートしようとしているすべてのプラットフォームに一貫したVM技術が存在しないという事実にまで手をつける必要があるのでしょうか？繰り返しになりますが、ユーザーからのフィードバックにより「それをしたい」ということが明らかになりました。だからこそ、私たちは挑戦しなければなりませんでした。

DANIEL WALSH: ソケットを開発する際に行ったことの一つは、すべてのPodmanコマンドがソケットに対してもローカルに対しても動作するようにすることでした。そのため、CI/CDシステムの開発ツールチェーン全体で、常にPodmanとPodman remoteと呼ばれるものをソケットに対してチェックし、インターフェースがまったく同じように動作することを確認しています。

私たちは以前からPodmanリモートを持っていました。Brentが言ったように、「podman-remote」があり、よくある標準的なLinux上のPodmanを使って、リモートマシンと会話することができます。ここで指摘したいのは、Macでは、PodmanリモートクライアントがリモートOS上のPodmanを通じてコンテナと対話しているのです。

WindowsとWSL2では、Microsoftは実にクールなことをしています。本物のPodmanをWindowsシェルやPowerShellの中で動かしています。つまり、WSLバージョンのPodmanを使っているときは、実際にはPodmanリモートを使っていないのです。どうやっているのかはわかりませんが、実際に動作します。多くのユーザーから、WSL2でのPodmanの動作がとても気に入っていると言われています。

---
## (45:13) Docker adjective
<!--
CRAIG BOX: You have a full set of tools here which now replace and provide all of the things that were provided by Docker. Where is Docker left? For example, you still create a file called Docker file. Is that the only place that string needs to still exist in everyone's workflow?

DANIEL WALSH: We have a file called containerfile that does the exact same thing.

CRAIG BOX: You’ve thought of everything.

DANIEL WALSH: I'm always telling people, how can we get rid of the Docker adjective? We have dockerignore, which is a way of building images. There's a special file called the dockerignore. So we also support, not only the dockerignore, we support containerignore. And we have dockerfile and containerfile. They're both the same thing.

So I'm always looking for, how do we get the Docker adjective out of things? We realize that we always have to support sort of the traditional way that everybody thinks of containers. We have to match and use those tools. As Brent says, we're always surprised when we add a new feature. One of the things we were surprised with in the last couple of weeks is our tools, we barely got them out the door, and everybody's using them, and everybody's happy with them. They work pretty well.

And then all of a sudden we start getting bug reports that say people on a Mac are running Docker Compose, and we didn't leak the Docker socket out of the VM onto the host, and they're disappointed that that doesn't work. We would never even have thought about that.

So suddenly we have all these new issues coming in about people wanting to do remote APIs from the Mac to sockets that are leaked out of VMs. So now we have to figure out how do we handle those types of issues. It is always an expanding world that we're having to deal with.

BRENT BAUDE: I think we see Docker references, Dan, still in some of the documentation for the common API, as well as the Docker schema for image formats and whatnot. That's still a capital-D Docker standard, if you will, referred to as a Docker schema.
-->

CRAIG BOX: さて、Dockerが提供していたものをすべて置き換えて提供するツールが揃いました。「Docker」という文言はどこへ行ったのでしょうか？例えば、多くの人はワークフローの中で「Dockerfile」というファイルをまだ作成しています。文字列が必要なのはそこだけでしょうか？

DANIEL WALSH: 全く同じことを行うことができる「Containerfile」というファイルがあります。

CRAIG BOX: あらゆることを考えていますね。

DANIEL WALSH: 私はいつも人々に「Docker」という形容詞をどうやって取り除くことができるかを話しています。イメージを構築する時に余計なものを取り除く「.dockerignore」という特別なファイルがありますが、私たちは「.dockerignore」だけでなく「.containerignore」もサポートしています。また、「Dockerfile」と「Containerfile」はこれらはどちらも同じものです。

私はいつもどうすればDockerという形容詞を物事から取り除くことができるのか探し続けています。私たちは誰もがコンテナについて考える従来の方法を常にサポートしなければならないことも、これらのツールが使い方にマッチしていなければならないことも理解しています。Brentが言うように、新しい機能を追加したときには、いつも驚かされます。ここ数週間で私たちが驚いたことのひとつは、私たちのツールが、やっとのことでリリースされ、みんなが使っていて、みんなが満足していることです。とてもよく機能しています。

そして突然、バグレポートを受け取り始めたのです。それによると、MacでDocker Composeを実行している人がいて、DockerソケットをVMからホストに公開していなかったので、それが機能しないことにがっかりしているというのです。私たちはそんなことは考えもしませんでした。

そのため、突然、VMから公開されたソケットに対してMacからリモートAPIを実行したいという新しい問題が発生しました。このような問題にどのように対処するかを考えなければなりません。私たちが対処しなければならない世界は常に拡大しています。

BRENT BAUDE: Danが言っているDockerの形容詞は、共通APIのドキュメントの一部においてイメージフォーマットなどの「Docker Schema」として見つけることができます。これはまだ大文字Dの「Docker」の標準規格で「Docker Schema」と呼ばれるものです。

---
## (47:11) Double edge sword
<!--
CRAIG BOX: You'll always have to have the "alias docker=podman" line in the docs, but that's got a lowercase d, so that's probably OK.

DANIEL WALSH: Docker Inc did a fabulous job of embedding their name into everything, but I think that also was a double edged sword for them. It's something that they've had a hard time dealing with and that they want to differentiate the Docker enterprise thing from this Docker open source tool.

Way back when I started at Red Hat, we had Red Hat Linux. At the time, Red Hat Linux was the thing we wanted to sell and the thing that we were giving away for free. Eventually, we split. We created Red Hat Enterprise Linux and then we created Fedora as two sort of separate entities. And that allowed us to really differentiate.

So while Docker was able to get their name everywhere, they sort of — almost like Kleenex, or I guess Xerox, everybody might refer to every box of tissues as a Kleenex. All of a sudden, everybody can sell tissues, and people don't really care whether or not it has the Kleenex name on the box or doesn't have the Xerox name on your copier machine. Those things are double edged swords, at times.

Personally, I don't like people calling them Podman containers. I'd prefer that everybody just call them containers. We don't need to start the Podman registry and the Podman image and the Podman container. These are containers and these are images and it's just—

CRAIG BOX: And then we'd have to start arguing about how you pronounce "Quay".

DANIEL WALSH: Exactly.
-->

CRAIG BOX: ドキュメントには常に「alias docker=podman」の行が必要になりますが、これは小文字のdなので、おそらく問題ないでしょう。

DANIEL WALSH: Docker社は自分たちの名前をすべてに埋め込むという素晴らしい仕事をしましたが、それは彼らにとって諸刃の剣でもあると思います。彼らはDockerエンタープライズ製品とこのDockerオープンソースツールとを差別化したいということに苦労しています。

私がRed Hatに入社した頃、私たちにはRed Hat Linuxがありました。当時、Red Hat Linuxは私たちが売りたいものであり、無料で提供していたものでした。その後、私たちはそれをRed Hat Enterprise LinuxとFedoraという2つの独立した形態に分割しました。これによって、私たちは本当に差別化できるようになりました。

Dockerはどこにでも名前を広めることができましたが、それはある意味、クリネックスやゼロックスのように、みんながティッシュの箱をクリネックスと呼んでいるようなものでした。ある時、誰もがティッシュを売ることができるようになると、人々は箱にクリネックスの名前が入っているかどうか、コピー機にゼロックスの名前が入っていないかどうかを気にすることはなくなりました。そういったことは、時として諸刃の剣になります。

個人的には、「Podmanコンテナ」と呼ばれるのは好きではありません。みんなが単にコンテナと呼んでくれればいいと思っています。PodmanレジストリやPodmanイメージ、Podmanコンテナなど呼ぶ必要はありません。これらはコンテナやイメージ、ただそれだけです。

CRAIG BOX: そうなると、「Quay」の発音をどうするかで議論しなければならなくなりますね。

DANIEL WALSH: そうですね。

---
## (48:34) Roadmap and priolity
<!--
CRAIG BOX: Brent, would you say that the roadmap for Podman has changed substantially in the last three weeks?

BRENT BAUDE: Some of the priorities have been swapped around, but all of the ideas were still in the queue. It definitely has been impressed upon this team, both internally and​​ externally, to continue to make the Mac solution the best we can, including a vision that would have a desktop experience.

There's some level of tightening up some things around the WSL, in terms of making certain little tweaks that make it fully functional, particularly in the areas of systemd.
-->
CRAIG BOX: ブレントさん、この3週間でPodmanのロードマップは大きく変わったと思いますか？

BRENT BAUDE: いくつかの優先順位は入れ替わりましたが、すべてのアイデアはまだキューに並んだままです。社内外を問わず、このチームには、デスクトップ体験のビジョンを含め、Macのソリューションを最高のものにし続けることが強く求められています。

特にsystemdの分野では、WSLを完全に機能させるためのちょっとした調整を行うなど、WSLの周辺をある程度強化しています。

---
## (49:13) Discussion with the community
<!--
CRAIG BOX: You've mentioned obviously there are a lot of engineers at Red Hat and a lot who are working on this, but I don't know how many Swift developers you may have on staff, for example. Is this an area that you're looking for community contribution?

BRENT BAUDE: Absolutely, and whether Swift is even the right approach, we're still having that conversation. Should it be something more JavaScriptish or HTML5ish that could be used in Linux as well as Mac? I think I'm the most qualified Swift programmer on the Podman team, and I have about 10 days of experience with it.

CRAIG BOX: I played with that little Swift Playgrounds tool on an iPad in an Apple Store for 10 minutes once, so I may be number 2. Who knows?

DANIEL WALSH: We had a meeting on September 16 with the community. We meet every third Thursday of the month, and we call it the Podman Cabal. What we're looking for is to really nail down the requirements of how we want Podman on a Mac to work. We're looking at what the GUI requirements are going to be, which sort of help us get priority on which features we want to get on Podman on a Mac.

We want to really look at that and pick out the best recommended way, and I’d love having lots of people doing lots of different ways of doing Podman, but the thing that comes out of the upstream Podman project should be the preferred way or the recommended way of running it. So that's what we're after

BRENT BAUDE: Dan makes an excellent point. It's something that I've been preaching with the team lately and people that I talked to in the community, in the sense that it's not necessarily good enough to mimic what Docker does. We really want to exceed that and add our own features and twists on top of that.

That's why I'm personally not compelled to just get something out in Swift and say, OK, now you've got a fancy icon that says it's running or it's not. I kind of like to look at a more whole picture from the community's perspective that actually uses this. We just write code. We're not necessarily the best container minds in the world with regards to some of this. It's certainly not in GUI.

CRAIG BOX: I will say it is very important that you have the icon there, so I can tell that the thing that's eating up all my RAM can be stopped with a single click.

BRENT BAUDE: We understand that aspect, but what are the other ones that really need to be there? And whether or not the one decision affects being able to do the other, and things like that are important to consider now. We don't have anything now, so now's the time to think about it and listen to what our users want and then make a decision and get rolling.

DANIEL WALSH: We had a lot of people opening up this full discussion in GitHub right now about adding a GUI to Podman for the Mac, and there's a lot of people coming in and saying they don't want a GUI for Podman. They want it to be separate because they just want the command line experience. They don't want any bells and whistles. They want to make sure that we allow them to install Podman without the GUI as well as Podman with the GUI.

I've been at Red Hat for 20 years, which means that I've been running on a Linux desktop for the last 20 years. And my opinion on what a Mac or a Windows experience should look like should be taken with a grain of salt. Let's put it that way. And so we really want to listen to people who live in that world all the time and match their expectations.
-->

CRAIG BOX: Red Hatには多くのエンジニアがいて、この分野に取り組んでいるとおっしゃいましたが、例えばSwiftの開発者がどれくらいいるのかはわかりません。この分野ではコミュニティの貢献を求めているのでしょうか？

BRENT BAUDE: もちろんです。Swiftが正しいアプローチであるかどうかは今も議論中です。MacだけでなくLinuxでも使用できるような、もっとJavaScriptやHTML5的なものにすべきかなどについて。Podmanチームの中でSwiftのプログラマーとしては私が一番適任だと思いますが、私は10日間ほどSwiftを使った経験があります。

CRAIG BOX: Apple StoreでiPad上の小さなSwift Playgroundsツールで10分ほど遊んだことがあるので、私は2番かもしれませんね、分かりませんが。

DANIEL WALSH: 9月16日にコミュニティとのミーティングを行いました。毎月第3木曜日に開催していて、「Podman Cabal」と呼んでいます。私たちはMac上のPodmanがどのように動作するのか、その要件を明確にしたいのです。GUIの要件を検討していますが、これはMac上のPodmanに搭載したい機能の優先順位を決めるのに役立ちます。

多くの人が様々な方法でPodmanを使ってくれるのは嬉しいことですが、アップストリームのPodmanプロジェクトから出てくるものが、推奨される方法、または推奨される実行方法であるべきです。それが私たちの目指すところです。

BRENT BAUDE: Danは素晴らしい指摘をしています。最近、私がチームやコミュニティの人たちに説いてきたことですが、Dockerの機能を模倣するだけでは必ずしも十分ではないということです。私たちは、Dockerの機能を超えて、その上に独自の機能や工夫を加えたいと考えています。

だからこそ、個人的にはSwiftで何かを作って、「さあ、実行中かそうでないかを示す派手なアイコンができました」と言いたいわけではないのです。私は、実際にこれを使用するコミュニティの視点から、より全体像を見たいのです。私たちはコードを書くだけです。私たちはコードを書いているだけで、このGUIの分野においてはでコンテナほど知見を持っているわけではありません。

CRAIG BOX: しかし、アイコンを表示することは非常に重要です。そうすれば、私のRAMを食いつぶしているものがわかり、ワンクリックで止められます。

BRENT BAUDE: その点は理解していますが、他に本当に必要なものは何でしょうか？また、1つの決定が他の決定に影響するかどうかなど、今考えておくべき重要なことがあります。今はまだ何もありませんから、今こそ考えて、ユーザーの要望を聞いて、決断して軌道に乗せていきたいと思います。

DANIEL WALSH: 今、GitHubでは、Mac用のPodmanにGUIを追加するための議論が行われていますが、多くの人が「PodmanのGUIはいらない」「コマンドラインでの操作がしたいから別物にしてほしい」「余計なものは要らない」と言っています。彼らは、GUIなしでPodmanをインストールすることも、GUI付きのPodmanをインストールすることもできるようにしたいと考えています。

私はRed Hatに20年間在籍していますが、この20年間はLinuxデスクトップで運用してきたことになります。そして、MacやWindowsのエクスペリエンスがどうあるべきかについての私の意見は、大目に見てください。言い方を変えると、だからこそ、その世界に常に身を置いている人たちの意見に耳を傾け、彼らの期待に応えたいと思っています。

---
## (52:28) Podman Community
<!--
CRAIG BOX: Where should people go if they want to be part of this process?

DANIEL WALSH: Podman.io is our website, and that's the easiest way to find out all information. A lot of our communication goes through GitHub, so GitHub issues, so github.com/containers/podman.

We're old school, we live on IRC. We're up on Libera.Chat in #podman, although that's all mirrored now inside — I'm told by these young'uns that it's mirrored through Matrix and Discord, whatever those things are. For these people who want it on their cell phone, I guess.

That's really where the engineers tend to live. And there's a Podman mailing list, too, available at podman.io.
-->

CRAIG BOX: このプロセスに参加したい人はどこに行けばいいですか？

DANIEL WALSH: Podman.ioは私たちのウェブサイトで、すべての情報を得るにはこれが一番簡単な方法です。私たちのコミュニケーションの多くはGitHubで行われています。GitHub issuesやgithub.com/containers/podman などです。

私たちは昔からIRCを使っています。Libera.Chatでは#podmanを使っていますが、今はすべて内部でミラーリングされています。若い人たちによると、MatrixやDiscordでミラーリングされているそうです。スマートフォンでで見たいという人たちのために、ですね。

そこにはエンジニアが多く集まっています。また、Podmanのメーリングリストもpodman.ioで公開されています。

---
## (53:05) Ending
<!--
CRAIG BOX: All right, Brent and Dan, thank you very much for joining us today.

BRENT BAUDE: Thank you.

DANIEL WALSH: Thanks for having us. That was great.

CRAIG BOX: You can find Dan on Twitter at @rhatdan, and you can find Brent at @bbaude. You can find Podman at podman.io.
-->

CRAIG BOX: さて、BrentとDan、今日はどうもありがとうございました。

BRENT BAUDE: ありがとうございます。

DANIEL WALSH: お招きいただきありがとうございました。よかったですね。

CRAIG BOX: Danのツイッターは@rhatdan、Brentのツイッターは@bbaudeでご覧いただけます。Podmanはpodman.ioでご覧いただけます。

---
[^1]: 音声の発音上は「zed」と発音。翻訳文は実際のコマンドに合わせて表記
[^2]: https://github.com/appc