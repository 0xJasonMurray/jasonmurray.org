---
title: "Fixing garbled terminal output with reset"
date: 2020-12-10T16:55:22-06:00
toc: false
images:
categories:
  - tech
tags: 
  - terminal
  - reset
---

Sometimes when binary data is displayed to a terminal session it will garble the shell prompt and turn this:

```
jemurray@mbp-2019:/usr/bin $
``` 

into this:

```
8g䭢����C0@���-��uL:/�Y�/��� $
```

For example, `cat`ing out `/dev/random`:


```
jemurray@mbp-2019:/usr/bin $ head /dev/random
�M��&n�0�/[x6��4��(i/��M۩�5����'�^��_��g�d�qC|\x�u��.��J�DNi��~ò?\玀�O�D��lC�O��+� C�����G��:�����2�rc���h�胚���1yo{`L�I�
                                                                                                                         3��$�γ��ʄ}��U�q�����'�\��X�h��t+��_��\z+���^e�.
                                                                                                                                                                         1>2]��4��ԅ&L^���K�n
�dY���� $�0��-~��(�
                   �o�͛������:
                             ���T���a�P��l(\h�fh   ��(��3�m��6mp�
                                                                  O�ｳ� �}��?K�;Zl�x����F������o�l|WD��NLtB�9�C�a!�(��Pi��gk;f���e��^A�+��h_M�Y�{�q�v[�;���g`W�M���oʷ��Z�w�
                                                                                                                                                                            9`�j����ǘ����R��U�J��{9����7G���
                     :
                      >����
�u>���o�0g�r{B"l�ZA�
}��t.�d��~u����lWk�-�T�Π$c���F>�����'����$F�\��@�!��־�^G��v}!"xba�Q�Y��    |6���!�cm'k?����or�|�
1�Bj�   ��w7��%��K#P���~��:y$�I�e�tӱ*���2��,(���9/C�7��`:�3V����"�Ȑ^��4`����~�kL/D]����-���H׺����Mk@w����T@K�bʴ,Cc����:��7HX���A�����k���Y�.�)TJli���hJk���O���TKkD �Wk�{L�D��q���=(�kY�h':����zm%��3|v6��=4���6����i��vð��Z������MW�
�>�*^p���-�ңHoS2/G���`�5+t-hd6tn@L�(�{��f"w��(�q�8������l�<��9\�^Ն�f6����;f��?
���w����kⴃ���ᜊ��b_*cu�IWF�D��PwG
a���#b�(�R��T����Q�s\�Ο7i�$}�-�al�����*�YTt�+����tL��:К�4]#��ً��~�%J*���,�!�}�Z�
                                      $�?����xß��c
                                                  �{K��jm
C�O�ֆ���ڡ�]�rU7޻Sm֕.�[���;���݁x1�}����Mn�� ��s}�B��4�p'w?�{�$e�<�_�U)�������-R:�RJcվ�ߏe��N�'�n�Vc�c⇱�w0j]�G˵� !rĉ�$�H��I�ᇠ��6;I�㽔�-��5a���'        ��_u4~1��u}rQ���09�J,������g� ���Jȝ����kP��ԩX���Ů1X��*D��Shw��D�����l;���=�G�����=a�F+*�E>L�!����l��Nt4�E5��t���T�h��s��
��_<<Glzp6��*�  ���A�hי.�R�A�ܨ��5BT��#�e��L4B��ї�mr|�@p�7�sO)��]D=0\�O�2I�+i�;�J-
��      M�cErAF����
                   �t�M����헧�4t�V�ǲ�����Ep{�=�']�B���`�0�C{�� }��v�h��>(0M�j�������uL0�u���JM�0��_co�@��
                                                                                                         >
                                                                                                          �-}��_8��L����
                                                                                                                        �!`��t0�
�Kb8g䭢����C0gUmk�7�]ѽ"�PB� !�                                                                                                  �&8��
z`      k�T����泟)a


8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
8g䭢����C0@���-��uL:/�Y�/��� $
```

To resolve this problem, type `reset` and press `<enter>`.  The screen will clear and the terminal prompt should be reset back to normal:

```
8g䭢����C0@���-��uL:/�Y�/��� $ reset
jemurray@mbp-2019:/usr/bin $
```
