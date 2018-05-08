^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Changelog for package collada_parser
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Forthcoming
-----------
* add exec_depend to package.xml of collada_parser for loading by pluginlib (`#27 <https://github.com/ros/collada_urdf/issues/27>`_)
* Contributors: Yohei Kakiuchi

1.12.11 (2018-04-17)
--------------------
* Collada cleanup dependencies (`#26 <https://github.com/ros/collada_urdf/issues/26>`_)
* update links now that this is in its own repo
* Make CMakeLists.txt depend on collada-dom version 2.4. (`#11 <https://github.com/ros/collada_urdf/issues/11>`_)
* Contributors: Chris Lalancette, Mikael Arguedas

1.12.10 (2017-05-04)
--------------------
* Moved collada_parser and collada_urdf to new repository

1.12.9 (2017-04-26)
-------------------

1.12.8 (2017-03-27)
-------------------
* add Chris and Shane as maintainers (`#184 <https://github.com/ros/robot_model/issues/184>`_)
* fix missed mandatory -std=c++11 flag (`#181 <https://github.com/ros/robot_model/issues/181>`_)
  collada_parser,kdl_parser,urdf: add c++11 flag,
  collada_parser: replace typeof with ansi __typeof\_\_
  builded/tested on gentoo
  Thanks den4ix for the contribution!
* Contributors: Denis Romanchuk, William Woodall

1.12.7 (2017-01-26)
-------------------

1.12.6 (2017-01-04)
-------------------
* Now using ``urdf::*ShredPtr`` instead of ``boost::shared_ptr`` (`#144 <https://github.com/ros/robot_model/issues/144>`_)
* Contributors: Jochen Sprickerhof

1.12.5 (2016-10-27)
-------------------

1.12.4 (2016-08-23)
-------------------

1.12.3 (2016-06-10)
-------------------

1.12.2 (2016-04-12)
-------------------

1.12.1 (2016-04-10)
-------------------

1.11.8 (2015-09-11)
-------------------

1.11.7 (2015-04-22)
-------------------

1.11.6 (2014-11-30)
-------------------
* fix rotation of joint axis when oriantation between parent link and child link is differ
* Contributors: YoheiKakiuchi

1.11.5 (2014-07-24)
-------------------

1.11.4 (2014-07-07)
-------------------
* collada_parser: add extract actuators, fix for using nominal torque
* moving to new dependency for urdfdom and urdfdom_headers. https://github.com/ros/rosdistro/issues/4633
* Contributors: Tully Foote, YoheiKakiuchi

1.11.3 (2014-06-24)
-------------------
* update usage of urdfdom_headers for indigo/trusty
* Contributors: William Woodall

1.11.2 (2014-03-22)
-------------------

1.11.1 (2014-03-20)
-------------------
* remove visual and collision if there is no vertices
* do not use visual and collision group
* fix debug message
* fix problem of root coordinates
* Contributors: YoheiKakiuchi

1.11.0 (2014-02-21)
-------------------
* fix, joint axis should be rotated depend on local coords
* fix overwriting velocity limit
* Contributors: YoheiKakiuchi

1.10.18 (2013-12-04)
--------------------
* add DEPENDS for kdl_parser
* Contributors: Ioan Sucan

1.10.16 (2013-11-18)
--------------------
* fix for using collada_parser_plugin

1.10.15 (2013-08-17)
--------------------
