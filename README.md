# model-machine-mushroom
Model Machine Learning for dataset mushroom using scikit learning

```jupyter
{
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "name": "model_mushroom.ipynb",
      "provenance": [],
      "collapsed_sections": []
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    },
    "language_info": {
      "name": "python"
    }
  },
  "cells": [
    {
      "cell_type": "code",
      "execution_count": 1,
      "metadata": {
        "id": "p3dHpTXkpZik"
      },
      "outputs": [],
      "source": [
        "import pandas as pd\n",
        "import seaborn as sns\n",
        "import matplotlib.pyplot as plt\n",
        "import numpy as np\n",
        "from typing import Optional, Any\n",
        "from sklearn.linear_model import LogisticRegression\n",
        "from sklearn.preprocessing import StandardScaler, LabelEncoder\n",
        "from sklearn.model_selection import GridSearchCV\n",
        "from sklearn.model_selection import train_test_split\n",
        "from sklearn import metrics\n",
        "from sklearn.dummy import DummyClassifier\n"
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "from google.colab import drive\n",
        "drive.mount('/content/drive', force_remount=True)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "zbBU7eSoVlBm",
        "outputId": "a1366d10-baeb-4f2f-f05e-5a7670a90797"
      },
      "execution_count": 2,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Mounted at /content/drive\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "path = '/content/drive/MyDrive/agaricus-lepiota.data.csv'"
      ],
      "metadata": {
        "id": "DQyW_0g9Wyet"
      },
      "execution_count": 3,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "\"\"\"\n",
        "อธิบายถึง features มีอะไรบ้าง เพราะในข้อมูลที่เขาให้มามันเป็นตัวย่อ เราต้องมาเปิด description ของเขาลองดูตารางด้านล่าง\n",
        "\n",
        "\n",
        "    1. cap-shape:                bell=b,conical=c,convex=x,flat=f,\n",
        "                                  knobbed=k,sunken=s\n",
        "     2. cap-surface:              fibrous=f,grooves=g,scaly=y,smooth=s\n",
        "     3. cap-color:                brown=n,buff=b,cinnamon=c,gray=g,green=r,\n",
        "                                  pink=p,purple=u,red=e,white=w,yellow=y\n",
        "     4. bruises?:                 bruises=t,no=f\n",
        "     5. odor:                     almond=a,anise=l,creosote=c,fishy=y,foul=f,\n",
        "                                  musty=m,none=n,pungent=p,spicy=s\n",
        "     6. gill-attachment:          attached=a,descending=d,free=f,notched=n\n",
        "     7. gill-spacing:             close=c,crowded=w,distant=d\n",
        "     8. gill-size:                broad=b,narrow=n\n",
        "     9. gill-color:               black=k,brown=n,buff=b,chocolate=h,gray=g,\n",
        "                                  green=r,orange=o,pink=p,purple=u,red=e,\n",
        "                                  white=w,yellow=y\n",
        "    10. stalk-shape:              enlarging=e,tapering=t\n",
        "    11. stalk-root:               bulbous=b,club=c,cup=u,equal=e,\n",
        "                                  rhizomorphs=z,rooted=r,missing=?\n",
        "    12. stalk-surface-above-ring: fibrous=f,scaly=y,silky=k,smooth=s\n",
        "    13. stalk-surface-below-ring: fibrous=f,scaly=y,silky=k,smooth=s\n",
        "    14. stalk-color-above-ring:   brown=n,buff=b,cinnamon=c,gray=g,orange=o,\n",
        "                                  pink=p,red=e,white=w,yellow=y\n",
        "    15. stalk-color-below-ring:   brown=n,buff=b,cinnamon=c,gray=g,orange=o,\n",
        "                                  pink=p,red=e,white=w,yellow=y\n",
        "    16. veil-type:                partial=p,universal=u\n",
        "    17. veil-color:               brown=n,orange=o,white=w,yellow=y\n",
        "    18. ring-number:              none=n,one=o,two=t\n",
        "    19. ring-type:                cobwebby=c,evanescent=e,flaring=f,large=l,\n",
        "                                  none=n,pendant=p,sheathing=s,zone=z\n",
        "    20. spore-print-color:        black=k,brown=n,buff=b,chocolate=h,green=r,\n",
        "                                  orange=o,purple=u,white=w,yellow=y\n",
        "    21. population:               abundant=a,clustered=c,numerous=n,\n",
        "                                  scattered=s,several=v,solitary=y\n",
        "    22. habitat:                  grasses=g,leaves=l,meadows=m,paths=p,\n",
        "                                  urban=u,waste=w,woods=d\n",
        "\n",
        "\"\"\""
      ],
      "metadata": {
        "id": "-A5diX27Zetv"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "dft = pd.read_csv(path)\n",
        "dft.head() # จะเห็นได้ว่าข้างล่างคือ features เกือบทั้งหมดยกเว้น จะมี target แค่ตัวเดียวคือ class ในข้อมูลจะใช้ตัวย่อ ลองเลื่อนไปอ่านข้างบน หรือไม่งั้นดาวโหลดไฟล์มาจากลิงค์ด้านบน"
      ],
      "metadata": {
        "id": "GDhbkjgapomq",
        "outputId": "b04bd578-1e7f-45d2-a64c-68b863efff1a",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 278
        }
      },
      "execution_count": 4,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>class</th>\n",
              "      <th>cap-shape</th>\n",
              "      <th>cap-surface</th>\n",
              "      <th>cap-color</th>\n",
              "      <th>bruises</th>\n",
              "      <th>odor</th>\n",
              "      <th>gill-attachment</th>\n",
              "      <th>gill-spacing</th>\n",
              "      <th>gill-size</th>\n",
              "      <th>gill-color</th>\n",
              "      <th>stalk-shape</th>\n",
              "      <th>stalk-root</th>\n",
              "      <th>stalk-surface-above-ring</th>\n",
              "      <th>stalk-surface-below-ring</th>\n",
              "      <th>stalk-color-above-ring</th>\n",
              "      <th>stalk-color-below-ring</th>\n",
              "      <th>veil-type</th>\n",
              "      <th>veil-color</th>\n",
              "      <th>ring-number</th>\n",
              "      <th>ring-type</th>\n",
              "      <th>spore-print-color</th>\n",
              "      <th>population</th>\n",
              "      <th>habitat</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>p</td>\n",
              "      <td>x</td>\n",
              "      <td>s</td>\n",
              "      <td>n</td>\n",
              "      <td>t</td>\n",
              "      <td>p</td>\n",
              "      <td>f</td>\n",
              "      <td>c</td>\n",
              "      <td>n</td>\n",
              "      <td>k</td>\n",
              "      <td>e</td>\n",
              "      <td>e</td>\n",
              "      <td>s</td>\n",
              "      <td>s</td>\n",
              "      <td>w</td>\n",
              "      <td>w</td>\n",
              "      <td>p</td>\n",
              "      <td>w</td>\n",
              "      <td>o</td>\n",
              "      <td>p</td>\n",
              "      <td>k</td>\n",
              "      <td>s</td>\n",
              "      <td>u</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>e</td>\n",
              "      <td>x</td>\n",
              "      <td>s</td>\n",
              "      <td>y</td>\n",
              "      <td>t</td>\n",
              "      <td>a</td>\n",
              "      <td>f</td>\n",
              "      <td>c</td>\n",
              "      <td>b</td>\n",
              "      <td>k</td>\n",
              "      <td>e</td>\n",
              "      <td>c</td>\n",
              "      <td>s</td>\n",
              "      <td>s</td>\n",
              "      <td>w</td>\n",
              "      <td>w</td>\n",
              "      <td>p</td>\n",
              "      <td>w</td>\n",
              "      <td>o</td>\n",
              "      <td>p</td>\n",
              "      <td>n</td>\n",
              "      <td>n</td>\n",
              "      <td>g</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>e</td>\n",
              "      <td>b</td>\n",
              "      <td>s</td>\n",
              "      <td>w</td>\n",
              "      <td>t</td>\n",
              "      <td>l</td>\n",
              "      <td>f</td>\n",
              "      <td>c</td>\n",
              "      <td>b</td>\n",
              "      <td>n</td>\n",
              "      <td>e</td>\n",
              "      <td>c</td>\n",
              "      <td>s</td>\n",
              "      <td>s</td>\n",
              "      <td>w</td>\n",
              "      <td>w</td>\n",
              "      <td>p</td>\n",
              "      <td>w</td>\n",
              "      <td>o</td>\n",
              "      <td>p</td>\n",
              "      <td>n</td>\n",
              "      <td>n</td>\n",
              "      <td>m</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>p</td>\n",
              "      <td>x</td>\n",
              "      <td>y</td>\n",
              "      <td>w</td>\n",
              "      <td>t</td>\n",
              "      <td>p</td>\n",
              "      <td>f</td>\n",
              "      <td>c</td>\n",
              "      <td>n</td>\n",
              "      <td>n</td>\n",
              "      <td>e</td>\n",
              "      <td>e</td>\n",
              "      <td>s</td>\n",
              "      <td>s</td>\n",
              "      <td>w</td>\n",
              "      <td>w</td>\n",
              "      <td>p</td>\n",
              "      <td>w</td>\n",
              "      <td>o</td>\n",
              "      <td>p</td>\n",
              "      <td>k</td>\n",
              "      <td>s</td>\n",
              "      <td>u</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>e</td>\n",
              "      <td>x</td>\n",
              "      <td>s</td>\n",
              "      <td>g</td>\n",
              "      <td>f</td>\n",
              "      <td>n</td>\n",
              "      <td>f</td>\n",
              "      <td>w</td>\n",
              "      <td>b</td>\n",
              "      <td>k</td>\n",
              "      <td>t</td>\n",
              "      <td>e</td>\n",
              "      <td>s</td>\n",
              "      <td>s</td>\n",
              "      <td>w</td>\n",
              "      <td>w</td>\n",
              "      <td>p</td>\n",
              "      <td>w</td>\n",
              "      <td>o</td>\n",
              "      <td>e</td>\n",
              "      <td>n</td>\n",
              "      <td>a</td>\n",
              "      <td>g</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "</div>"
            ],
            "text/plain": [
              "  class cap-shape cap-surface  ... spore-print-color population habitat\n",
              "0     p         x           s  ...                 k          s       u\n",
              "1     e         x           s  ...                 n          n       g\n",
              "2     e         b           s  ...                 n          n       m\n",
              "3     p         x           y  ...                 k          s       u\n",
              "4     e         x           s  ...                 n          a       g\n",
              "\n",
              "[5 rows x 23 columns]"
            ]
          },
          "metadata": {},
          "execution_count": 4
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "sns.set_style('darkgrid')\n",
        "plt.figure()\n",
        "fig1 = sns.countplot(dft['class'], alpha=1, palette= ['red','green'])\n",
        "plt.title('Edible vs Poisonous')\n",
        "plt.ylabel('# Amount')\n",
        "plt.xlabel('Class')\n",
        "fig1.set(xticklabels=['Poisonous', 'Edible'])\n",
        "plt.show()\n"
      ],
      "metadata": {
        "id": "4kgEdaoNr1dr",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 352
        },
        "outputId": "b66eb339-c64a-424c-c826-ba70afc4fe34"
      },
      "execution_count": 5,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "iVBORw0KGgoAAAANSUhEUgAAAYsAAAEWCAYAAACXGLsWAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADh0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uMy4yLjIsIGh0dHA6Ly9tYXRwbG90bGliLm9yZy+WH4yJAAAgAElEQVR4nO3de1hVdd7//+dmI4XKSYWNOgyJWTp56IAH1GIG3aICggqWlaVR2ah5exgb1DsvpUKbqVsra5Rp6rbDVXeecJLMA5rApJmVUY1eM5Z4ZuNwEMUS2K7fH37bv0hgabAB8fW4Lq5r78/6rM96L6+PvFhr7b2WxTAMAxERkTp4NHUBIiLS/CksRETElMJCRERMKSxERMSUwkJEREwpLERExJTCQq5Kx44d4+abb6aqqgqAhx9+mPXr1wOwbt06xo8fX+u6EyZMYPXq1Y1SZ0P66T6KNDbPpi5A5EdRUVH85z//wWq1utpGjx7NggULTNd99dVX3Vlag5owYQL79u3D09MTLy8v+vbty4IFCwgKCqpzvatpH6XlUVhIs7JixQoGDhzY1GW43YIFC0hKSqK0tJTp06ezePFili5d2tRlidRKp6HkquB0Onn22Wfp378/Q4YMYefOndWW//zUkmEYpKamcscddzB8+HB27dpV69hr1qxhxIgR9O3bl+TkZI4fP15jv4cffpi33nqrWtuoUaPYsmULhmGQlpZGREQEt99+O3FxcfzrX/8y3S9/f3+io6P597//DcDnn3/O2LFjueOOOxg7diyff/55jft4+PBh7r//fu644w769+/PjBkzXP3Mxli2bBn33HMPt912Gw899BDFxcWu5VlZWcTExBAeHs6ECRP49ttvXctuvvlmDh8+7HqfkpLiCrji4mImT55MeHg4/fr149577+XChQum+y9XD4WFXBXee+89duzYQUZGBmvXruXDDz+ss39eXh6//vWv2b17N9OnT2fatGmUlpZe0m/btm2sXLmS5cuXs2vXLu644w5mz55d45ixsbFs3LjR9f7gwYOcOHGC3/72t+Tm5rJ37142b97MZ599xrJly/D39zfdr+LiYjZv3kyPHj0oLS1l8uTJTJgwgU8++YRJkyYxefJkSkpKLlnvhRdeYNCgQXz66adkZ2dz//33A1zWGBs3bmTx4sXs2rWL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0L59ewoLCwEoLCykXbt2wMUjhoKCAte6BQUF2Gy2S9odDgc2m+2K6hARkfqpNSw2bNjAsmXLKC8vZ+LEiYwfP57y8vLLurgNYBgG8+fPJywsjEmTJrnao6KiyMjIACAjI4MhQ4ZUazcMg3379uHj40NQUBCDBw8mNzeX06dPc/r0aXJzcxk8eHB99llERK6QxTAM43I6fv3112RmZrJp0yaCg4N599136+y/d+9e7rvvvmrf/J41axa9e/dmxowZnDx5kk6dOrFs2TL8/f0xDIPU1FRycnLw9vYmLS2NXr16ARcf7bpy5UoAHnvsMdOHL1VWOhvkNFR+ly71GkNanhsOHeLUqaa/V1pgoA8BMwOaugxpZkqWltRrftZ1Guqyw+JHhmGwd+/eK77I3ZgUFuIuCgtpztwZFld8syeLxdKsg0JERBqe7gwoIiKmFBYiImLKNCxeeeUV1+uKigq3FiMiIs1TrWGRnp7OF198webNm11td999d6MUJSIizUut3+AOCwvjww8/5OjRo9x7772EhYVRWlrKd999R1hYWGPWKCIiTazWIwtfX19mzZpFaGgob775pusBSH/961+55557Gq1AERFperUeWeTm5vLyyy9z5MgRFi9ezM0334y3tzeLFy9uzPpERKQZqPXIYtasWaxatYrOnTsTHx/PhQsXKC4uZvz48Tz22GONWaOIiDQx04cfDR48mF69etGrVy/eeecd3nnnHYqLixujNhERaSZMPzr7xBNPuF4vWbIEwHWnWBERuTZc0Zfyunfv7q46RESkGdM3uEVExJTCQkRETCksRETElMJCRERMuS0s5s6dS0REBLGxsa62l156iTvvvJP4+Hji4+PZuXOna9nKlSux2+1ER0eTk5Pjas/OziY6Ohq73U56erq7yhURkTqYfs/ilxozZgz3338/f/zjH6u1T5w4keTk5GptBw8eJDMzk8zMTBwOB5MmTXLdwDA1NZXXX38dm81GYmIiUVFR3Hjjje4qW0REauC2sOjbty/Hjh27rL5ZWVnExMTg5eVFSEgIoaGh5OXlARAaGkpISAgAMTExZGVlKSxERBqZ28KiNm+//TYZGRn07NmTlJQU/Pz8cDgc9OnTx9XHZrPhcDgACA4Ortb+Y4jUxWq14O/fuuGLFwHNLWnW3DU/GzUsxo8fz5QpU7BYLLzwwgssWbLELTcmdDoNSkvP1WuMuh5cLte2+s6thqD5KbWpz/ysa1416qehOnTogNVqxcPDg6SkJL766ivg4hFDQUGBq5/D4cBms9XaLiIijatRw6KwsND1etu2bXTr1g2AqKgoMjMzqaio4OjRo+Tn59O7d2969epFfn4+R48epaKigszMTKKiohqzZBERwY2noWbNmsWePXsoKSnhrrvu4vHHH2fPnj0cOHAAgM6dO5OamgpAt27dGDFiBCNHjsRqtbJgwQKsVisACxYs4OGHH8bpdDJ27FhXwIiISOOxGIZhNHURDa2y0tkg1yzyu3RpoIqkpbjh0CFOnTrT1GUQGOhDwMyApi5DmpmSpSX1mp/N5pqFiIhcnRQWIiJiSmEhIiKmFBYiImJKYSEiIqYUFiIiYkphISIiphQWIiJiSmEhIiKmFBYiImJKYSEiIqYUFiIiYkphISIiphQWIiJiSmEhIiKm3BYWc+fOJSIigtjYWFdbaWkpkyZNYtiwYUyaNInTp08DYBgGTz/9NHa7nbi4OL755hvXOuvXr2fYsGEMGzaM9evXu6tcERGpg9vCYsyYMbz66qvV2tLT04mIiGDLli1ERESQnp4OQHZ2Nvn5+WzZsoWnnnqKhQsXAhfDZfny5bz33nusXr2a5cuXuwJGREQaj9vCom/fvvj5+VVry8rKIiEhAYCEhAS2bdtWrd1isXDrrbdSVlZGYWEhubm5DBo0CH9/f/z8/Bg0aBA5OTnuKllERGrRqNcsioqKCAoKAiAwMJCioiIAHA4HwcHBrn7BwcE4HI5L2m02Gw6HozFLFhERwLOpNmyxWLBYLG4Z22q14O/f2i1ji2huSXPmrvnZqGHRvn17CgsLCQoKorCwkHbt2gEXjxgKCgpc/QoKCrDZbNhsNvbs2eNqdzgc9OvXz3Q7TqdBaem5etVa14PL5dpW37nVEDQ/pTb1mZ91zatGPQ0VFRVFRkYGABkZGQwZMqRau2EY7Nu3Dx8fH4KCghg8eDC5ubmcPn2a06dPk5uby+DBgxuzZBERwY1HFrNmzWLPnj2UlJRw11138fjjj/Poo48yY8YM1qxZQ6dOnVi2bBkAkZGR7Ny5E7vdjre3N2lpaQD4+/szZcoUEhMTAZg6dSr+/v7uKllERGphMQzDaOoiGlplpbNBTkPld+nSQBVJS3HDoUOcOnWmqcsgMNCHgJkBTV2GNDMlS0vqNT+bzWkoERG5OiksRETElMJCRERMKSxERMSUwkJEREwpLERExJTCQkRETCksRETElMJCRERMKSxERMSUwkJEREwpLERExJTCQkRETCksRETElMJCRERMKSxERMRUoz6D+0dRUVG0adMGDw8PrFYr69ato7S0lJkzZ3L8+HE6d+7MsmXL8PPzwzAMnnnmGXbu3Mn111/PkiVLuOWWW5qibBGRa1aTHVmsWrWKDRs2sG7dOgDS09OJiIhgy5YtREREkJ6eDkB2djb5+fls2bKFp556ioULFzZVySIi16xmcxoqKyuLhNSqj4UAAAl9SURBVIQEABISEti2bVu1dovFwq233kpZWRmFhYVNWaqIyDWnSU5DASQnJ2OxWLj77ru5++67KSoqIigoCIDAwECKiooAcDgcBAcHu9YLDg7G4XC4+tbEarXg79/avTsg1yzNLWnO3DU/myQs3nnnHWw2G0VFRUyaNImwsLBqyy0WCxaL5ReP73QalJaeq1eNdT24XK5t9Z1bDUHzU2pTn/lZ17xqktNQNpsNgPbt22O328nLy6N9+/au00uFhYW0a9fO1begoMC1bkFBgWt9ERFpHI0eFufOnePs2bOu1//4xz/o1q0bUVFRZGRkAJCRkcGQIUMAXO2GYbBv3z58fHzqPAUlIiINr9FPQxUVFTF16lQAnE4nsbGx3HXXXfTq1YsZM2awZs0aOnXqxLJlywCIjIxk586d2O12vL29SUtLa+ySRUSueRbDMIymLqKhVVY6G+SaRX6XLg1UkbQUNxw6xKlTZ5q6DAIDfQiYGdDUZUgzU7K0pF7zs9ldsxARkauLwkJEREwpLERExJTCQkRETCksRETElMJCRERMKSxERMSUwkJEREwpLERExJTCQkRETCksRETElMJCRERMKSxERMSUwkJEREwpLERExJTCQkRETF01YZGdnU10dDR2u5309PSmLkdE5JpyVYSF0+kkNTWVV199lczMTDZu3MjBgwebuiwRkWvGVREWeXl5hIaGEhISgpeXFzExMWRlZTV1WSIi1wzPpi7gcjgcDoKDg13vbTYbeXl5tfZv1cpa57NkL9cNhw7VewxpeRpibjWEkqUlTV2CNEPump9XxZGFiIg0rasiLGw2GwUFBa73DocDm83WhBWJiFxbroqw6NWrF/n5+Rw9epSKigoyMzOJiopq6rJERK4ZV8U1C09PTxYsWMDDDz+M0+lk7NixdOvWranLEhG5ZlgMwzCauggREWnerorTUCIi0rQUFiIiYkph0QL06NGD+Ph4YmNjmT59Ot9//32tfbOysnS7FGl2fpzDP/7UNEc/+eQTJk+eDFSfxykpKXz44Yd19pf6uyoucEvdrr/+ejZs2ADA7Nmzeffdd5k0aVKNfYcMGcKQIUMaszwRUz+dw5dD87jx6ciihQkPD+fw4cOUlpYyZcoU4uLiGDduHAcOHABg3bp1pKamArBp0yZiY2MZNWoU9913HwDnz59n7ty5xMXFkZCQwO7du13rTZs2jeTkZIYNG8af/vQn1zY3btxIXFwcsbGx/PnPf3a133bbba7XH374ISkpKbVuV6Qm2dnZDB8+nNGjR7N161ZX+0/nMcDHH3/MmDFjiI6OZseOHZeMc+7cOebOnUtiYiIJCQls27atUepvSXRk0YJUVVWRnZ3NnXfeyUsvvcRvfvMbXnnlFXbt2sUf//jHS/5ye+WVV/jb3/6GzWajrKwMgLfffhuA999/n2+//Zbk5GQ2b94MwP79+8nIyMDLy4vhw4czYcIEPDw8eO6551i3bh2+vr489NBDbNu2jaFDh9ZaZ03blWvbDz/8QHx8vOv95MmTGTJkCE8++SSrVq0iNDSUGTNm1Lr+8ePHWbNmDUeOHOGBBx5g4MCB1ZavWLGCAQMGsHjxYsrKykhKSmLgwIG0bt3abfvU0igsWoCf/kcLDw8nMTGRcePG8dJLLwEQERFBaWkpZ8+erbbebbfdRkpKCiNGjMButwPw2Wefcf/99wPQtWtXOnXqxKH/d4+siIgIfHx8XMuOHz9OaWkp/fr1o127dgDExcXx6aef1hkWNW1Xrm01nYbav38/v/rVr7jhhhsAGDVqFO+9916N648YMQIPDw9uuOEGQkJC+O6776otz83NZfv27bz22mvAxSPokydP0rVr14bfmRZKYdECXOn53h+lpqby5Zdf8tFHHzF27FjWrl1bZ38vLy/Xa6vVitPpvOxtnT9/vs7tBgQEXHH9Ij+yWCx1vgd48cUXCQsLa6ySWhxds2ihwsPD+fvf/w5c/FRIQEAAbdu2rdbnyJEj9OnTh//6r/8iICCAgoICwsPDef/99wE4dOgQJ0+erPM/WO/evfn0008pLi7G6XSSmZlJ3759AejQoQPffvstFy5cqHaOuKbtivxcWFgYx48f58iRIwBkZmbW2vfDDz/kwoULHDlyhKNHj9KlS5dqywcPHsxbb73Fj99B/uc//+m+wlsoHVm0UNOmTWPevHnExcXh7e3NkiVLLunzpz/9icOHD2MYBgMGDKB79+6EhYWxcOFC4uLisFqtLF68uNoRxc8FBQUxe/ZsHnzwQQzDIDIy0nUKavbs2UyePJl27drRs2dPzp07V+t25dr282sWd955J3/4wx9ITU3l0UcfxdvbmzvuuIPy8vIa1+/YsSOJiYmUl5ezaNEirrvuumrLp0yZQlpaGqNGjeLChQv86le/YuXKlW7dp5ZGt/sQERFTOg0lIiKmFBYiImJKYSEiIqYUFiIiYkphISIiphQWIvV06tQpZs6cydChQxkzZgyPPPIIhw4dIjY2tqlLE2kw+p6FSD0YhsG0adNISEhg6dKlABw4cICioqImrkykYSksROph9+7deHp6Mn78eFdb9+7dOXbsmOv9sWPHeOKJJ1zPGXnyySe5/fbbKSwsZObMmZw9exan08nChQu57bbbmD9/Pl9//TUWi4WxY8cyceLExt4tkUsoLETq4d///je33HJLnX3at2/P66+/znXXXUd+fj6zZs1i3bp1bNy4kcGDB/P73/8ep9PJ999/z/79+3E4HGzcuBFAd+WVZkNhIeJmVVVVpKamcuDAATw8PMjPzwegV69ezJs3j6qqKoYOHUqPHj0ICQnh6NGjPPXUU0RGRjJ48OCmLV7k/9EFbpF66NatG998802dff73f/+XDh06sGHDBtauXUtlZSUAffv25a233sJms5GSkkJGRgZ+fn5s2LCBfv368e677zJ//vzG2A0RUwoLkXoYMGAAFRUV/N///Z+r7cCBA9XupHvmzBkCAwPx8PBgw4YNrlu7Hz9+nA4dOjBu3DiSkpL45ptvKC4uxjAMoqOjmTFjhu6OKs2GTkOJ1IPFYmH58uWkpaXx17/+leuuu47OnTszb948V597772Xxx9/nIyMDO68807X09n27NnD3/72Nzw9PWndujXPPvsshYWFzJ07lwsXLgAwa9asJtkvkZ/TXWdFRMSUTkOJiIgphYWIiJhSWIiIiCmFhYiImFJYiIiIKYWFiIiYUliIiIip/w+SjPCt3AndNQAAAABJRU5ErkJggg==\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "_class = dft['class'] # แยกคลาสออกจาก features\n",
        "df = dft.drop('class', axis=1) # เอาคอลัมน์ออก เหลือแต่ class\n",
        "df = df.replace(np.nan, regex=True) # นำข้อมูล value ที่ว่างออกเพื่อให้ชัวต่อการนำไปใช้งาน\n"
      ],
      "metadata": {
        "id": "ZMqtEj6iXhXK"
      },
      "execution_count": 6,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "def feature_plot(col: Optional[str] = None) -> Any:\n",
        "    \"\"\"\n",
        "    count feature\n",
        "    :param\n",
        "        - col\n",
        "    \"\"\"\n",
        "    sns.set_style('white')\n",
        "    plt.figure()\n",
        "    sns.countplot(df[col], alpha=1)\n",
        "    plt.ylabel('# Amount')\n",
        "    plt.title(f'Mushroom {col}')\n",
        "    plt.xlabel(f'Type of {col}')\n",
        "    plt.show() \n"
      ],
      "metadata": {
        "id": "8l-OkGM-XjHQ"
      },
      "execution_count": 7,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "feature_plot('cap-shape') # รูปทรงหมวด\n",
        "feature_plot('cap-color') # สีหมวก\n",
        "feature_plot('odor') # กลิ่น\n",
        "feature_plot('gill-color') # สีเหงือก อยู่ใต้หมวก\n",
        "feature_plot('habitat') # ที่อยู่อาศัย\n",
        "feature_plot('stalk-root') # ก้านราก\n",
        "feature_plot('spore-print-color') # สีพิมพ์สปอร์\n",
        "feature_plot('cap-surface') # พื้นผิว\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 1000
        },
        "id": "VUk1GABmXk__",
        "outputId": "65fd4418-154d-4fbf-839b-ea1ff651ca8c"
      },
      "execution_count": 8,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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wbFjx7Db7fj7+/PTTz9VW/bw4cPntRdUmzZt2lQ7l/Jr06dPZ/DgwWRmZrJ7925Gjx6N+Y8bRB8+fNj1+qeffnLtWX322Weu/9q2bUuDBg146KGHWL9+Pe+++y7btm2rdr6oNmvXriUjI4M33niD3bt388EHHwBUy1FQUOB6XVZWRklJCf7+/rRp04a+ffuya9cu13+fffYZzzzzzLl/QXJJUVmIWz377LMsX76cJk2anDXtxhtvZMuWLZw8eZIffviBVatWuabl5OTw+eefU1lZSePGjfH29nb9y/d8xcTEsHr1avbv309FRQUvvvgiPXr04NprryUsLIzc3FzWrl1LVVUV69ev5+DBg9x6660X+pFdYmNj+fDDD1m/fj1VVVUUFxe7DgGVlZXh6+tLw4YNycnJqfEQ06uvvsrJkyf55ptvWL16NcOGDatxOx9//DFfffUVTqeTZs2a4eXldU7fVVlZGd7e3rRo0YKTJ0/y4osvnjVPZmYmu3btoqKigv/5n//h5ptvpk2bNtx6663k5uaSmppKZWUllZWV5OTk8O23357ntySXCpWFuNV1111H9+7da5x2zz330KBBA/r378/jjz9ObGysa1pZWRl/+tOf6NevH7fddht+fn5MmDDhgjL079+fRx55hIcffpjQ0FDy8vJcP+pr0aIFr732Gm+88QbBwcEsW7aM1157jZYtW17Qtn6tbdu2/PWvf+WNN96gX79+xMfHc+DAAQCeeuopFi1aRK9evXjllVcYOnToWcv369ePiIgIkpKSuPfeewkNDa1xO0eOHGHKlCn07t2bYcOG0a9fv3M67BcfH0/btm0ZOHAg0dHR9OzZ86x5YmJieOWVVwgODmbfvn288MILADRr1oy//e1vrF+/noEDBxIaGsqCBQuoqKg4n69ILiE285/7tiJyScvPz2fw4MHs27cPLy/3nXZMSUnBbrczbdo0t2WQ+qM9CxERsaSyEBERSzoMJSIilrRnISIilq7IH+UFBwcTGBjo7hgiIpeVQ4cO8cknn9Q47Yosi8DAQNcN2URE5NwkJCTUOk2HoURExJLKQkRELKksRETEkspCREQsqSxERMSSykJERCypLERExJLKQkRELKksRETE0lVVFuWVTndHOG+XY2YRufJckbf7qE3DBp70fvTv7o5xXna/cLe7I4iIXF17FiIicmFU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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "iVBORw0KGgoAAAANSUhEUgAAAYsAAAEWCAYAAACXGLsWAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADh0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uMy4yLjIsIGh0dHA6Ly9tYXRwbG90bGliLm9yZy+WH4yJAAAgAElEQVR4nO3deVxU5f4H8M+wKSoKogyBy70uuKCGhhJKch1l1GAAWW5umGiRS5rJT5HsouFa96bmdg01L3XNckFQcScFMdOXphIuJRYKKsMVARWN9fn90fVcSfCgOWcQPu/Xq9dr5jnL850TzafnLM+ohBACREREj2Fi7AKIiKj2Y1gQEZEshgUREcliWBARkSyGBRERyWJYEBGRLIYF1XmzZs3C0qVLjV3Gcy8uLg4jRowwdhlkJAwLMjqNRoNu3brh1q1bldr9/f3RqVMnZGdnG6kyInqAYUG1gqOjIxITE6X3P/74I+7fv694HWVlZYr3WdcJIVBRUWHsMugPYlhQreDn54f4+HjpfXx8PPz9/SutExISgi1btkjvHz4tIoTAwoUL4e7ujl69ekGn0+Gnn36S1r19+zbCwsLQs2dPBAcH4+rVq9KyTp06YePGjdBqtdBqtQCAzZs3w8vLC3369MGECROg1+ul9b///nsEBgbipZdeQmBgIL7//vtKNS5duhTDhw9Hz549MWHCBOTn5yM8PBy9evVCYGDgY0dKJ0+exPDhw+Hq6gpPT0/ExcUBAA4fPgx/f3/06tULnp6eWLFihbRNdnY2OnXqhK+//hoeHh7w8PDA+vXrH3u8q+vnzp07mDlzJl5++WUMGDAAq1evrvaLvqbH4cUXX0RWVtZj66HngCAysgEDBoijR48KrVYrMjIyRFlZmXjllVdEdna2cHJyEllZWUIIIUaPHi02b94sbbdt2zYxfPhwIYQQKSkpYtiwYaKwsFBUVFSIjIwModfrhRBCREREiD59+oizZ8+K0tJSMX36dDFt2jRpP05OTmLs2LEiPz9f3L9/X3z77beiT58+Ij09XRQXF4vo6GgxcuRIIYQQ+fn5wtXVVWzfvl2UlpaKnTt3CldXV3Hr1i2pxkGDBokrV66I27dvi6FDhwqtViuOHj0qSktLxYwZM8SsWbOqPA7Z2dnCxcVF7Ny5U5SUlIhbt26J8+fPCyGE+O6778TFixdFeXm5uHDhgnB3dxcHDhwQQgiRlZUlnJycxLvvviuKiorExYsXhZubmzh69OgT9zNjxgwxYcIEcefOHZGVlSW0Wq10zB8+3jU5Dp6enuKnn34SpaWloqSk5In+Jqj24ciCao0Ho4ujR4+iffv2UKvVNd7WzMwMRUVF+PnnnyGEQPv27WFnZyctHzRoEHr06AEzMzP4+vriwoULlbYPCwuDtbU1GjZsiJ07dyIwMBDOzs6wsLDA9OnTcebMGWRnZ+Pw4cNo27Yt/P39YWZmBh8fH7Rr1w6HDh2S9hUQEIA2bdrAysoK/fv3R+vWrdG3b1+YmZlhyJAhOH/+fJWfYdeuXejbty98fHxgbm4OGxsbdOnSBQDg5uaGTp06wcTEBJ07d4a3tzdOnDhRafvJkyejUaNG6NSpEwICArBr164n6qe8vBy7d+9GeHg4mjRpglatWiE0NBQ7dux4ZB81OQ7Dhg1Dx44dYWZmBnNzc5l/g1TbmRm7AKIH/Pz8MHr0aGRnZ8PPz++JtnV3d8eoUaMQHR2Na9euQavVIiIiAk2aNAEAtGjRQlq3YcOGuHfvXqXtX3jhBel1bm4unJ2dpfeNGzeGtbU19Ho9cnNz4eDgUGlbBweHSqepHu6rQYMGsn0/cOPGDbRp06bKZWfPnsU//vEPXLp0CaWlpSgpKcGQIUOq/QyOjo7SabiePXtK7YmJidX2k5+fj9LS0kqf7/ef7YGaHIeH66HnH0cWVGs4OjqiVatWSE5Olq4dPMzS0rLSRe+bN29WWj5mzBjExcVh9+7dyMzMxLp162rct0qlkl7b2dnh2rVr0vt79+6hoKAAarUadnZ2uH79eqVtb9y48USjoOq88MILla6lPCw8PBwDBw5EcnIyTp06heHDh0P8bsLoGzduSK+vX78ujaxOnz4t/ePg4FBtPzY2NjA3N6/0+ar7bDU5Dg8fU3r+MSyoVlmwYAFiY2PRqFGjR5Z16dIFBw4cwP3793HlyhVs3bpVWpaWloazZ8+itLQUlpaWsLCwgInJ0/15+/j4IC4uDhcuXEBJSQmWLFmCHj16oFWrVvD09ERmZiZ27tyJsrIy7N69GxkZGfjLX/7ytB9ZotPp8O2332L37t0oKytDfn6+dLqsqKgIzZo1Q4MGDZCWllblKabVq1fj/v37uHTpEuLi4vDqq68+UT+mpqYYMmQIli5dirt37+LatWvYsGEDfH19H9mHIY8D1U4MC6pV2rRpg+7du1e57PXXX4e5uTn69u2LiIgI6HQ6aVlRURHef/999OnTBwMGDIC1tTXGjx//VDX07dsX77zzDqZMmQIPDw9kZWVJD/XZ2NhgzZo12LBhA9zc3LBu3TqsWbMGzZs3f6q+Hubg4IC1a9diw4YN6NOnD/z9/XHx4kUAwJw5c7B8+XL07NkTq1atwtChQx/Zvk+fPvDy8sLYsWMxbtw4eHh4PHE/f/vb32BpaYlBgwZh5MiR8PHxQWBg4CP7MORxoNpJJX4/liWi50p2djYGDhyIc+fOwcyMlyHJMDiyICIiWQwLIiKSxdNQREQkiyMLIiKSVSevhrm5ucHR0dHYZRARPVeuXbuG48ePV7msToaFo6OjNDEaERHVTEBAQLXLeBqKiIhkMSyIiEgWw4KIiGQxLIiISBbDgoiIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGTVi7AoLi2vk30RESmlTk738XsNzE3x0ozPFenr1N/HKNIPEZGS6sXIgoiI/hiGBRERyWJYEBGRLIYFERHJYlgQEZEshgUREcliWBARkSyGBRERyWJYEBGRLIYFERHJYlgQEZEshgUREcliWBARkSyGBRERyWJYEBGRLIYFERHJYlgQEZEshgUREcliWBARkSyGBRERyTJYWNy4cQMhISF49dVX4e3tjdjYWABAQUEBQkNDodVqERoaisLCQgCAEALz58+Hl5cXdDodzp07J+1r+/bt0Gq10Gq12L59u6FKJiKiahgsLExNTTFr1izs3r0bX3/9Nb788ktkZGQgJiYG7u7u2L9/P9zd3RETEwMASElJQWZmJvbv34958+Zh7ty5AH4Ll5UrV2Lz5s3YsmULVq5cKQUMEREpw2BhYWdnB2dnZwBAkyZN0K5dO+j1eiQlJcHf3x8A4O/vj4MHDwKA1K5SqeDi4oLbt28jNzcXqamp6NevH6ytrdGsWTP069cPR44cMVTZRERUBUWuWWRnZ+PChQt48cUXkZeXBzs7OwBAy5YtkZeXBwDQ6/Wwt7eXtrG3t4der3+kXa1WQ6/XK1E2ERH9l8HDoqioCFOnTsV7772HJk2aVFqmUqmgUqkMXQIREf1BBg2L0tJSTJ06FTqdDlqtFgBga2uL3NxcAEBubi6aN28O4LcRQ05OjrRtTk4O1Gr1I+16vR5qtdqQZRMR0e8YLCyEEJg9ezbatWuH0NBQqV2j0SA+Ph4AEB8fj4EDB1ZqF0LgzJkzsLKygp2dHTw8PJCamorCwkIUFhYiNTUVHh4ehiqbiIiqYGaoHZ86dQoJCQlwcnKCn58fAGD69OkICwvDtGnTsHXrVjg4OGDZsmUAAE9PTyQnJ8PLywuWlpZYuHAhAMDa2hqTJk1CUFAQAGDy5MmwtrY2VNlERFQFg4WFq6srfvzxxyqXPXjm4mEqlQpz5sypcv2goCApLIiISHl8gpuIiGQxLIiISBbDgoiIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGQxLIiISBbDgoiIZDEsSHEVxcV1si+iusxg030QVcekQQMk9/dUpC/PlGRF+iGq6ziyICIiWQwLIiKSxbAgIiJZDAsiIpLFsCAiIlkMCyIiksWwICIiWQwLIiKSxbAgIiJZDAsiIpLFsCAiIlkMCyIiksWwICIiWQwLIiKSxbAgIiJZDAsiIpLFsCAiIlkMCyIiksWwICIiWQwLIiKSxbAgIiJZDAsiIpLFsCAiIlkMCyIiksWwICIiWQwLIiKSZbCwiIyMhLu7O3x8fKS2FStW4JVXXoGfnx/8/PyQnJwsLfv000/h5eWFwYMH48iRI1J7SkoKBg8eDC8vL8TExBiqXCIiegwzQ+04ICAAo0ePRkRERKX2sWPHYvz48ZXaMjIykJiYiMTEROj1eoSGhmLfvn0AgOjoaGzYsAFqtRpBQUHQaDTo0KGDocomIqIqGCwsevfujezs7Bqtm5SUBG9vb1hYWKB169Zo27Yt0tLSAABt27ZF69atAQDe3t5ISkpiWBARKUzxaxYbN26ETqdDZGQkCgsLAQB6vR729vbSOmq1Gnq9vtp2omehrLS8TvZFZAgGG1lUZcSIEZg0aRJUKhU++eQTLF68GIsWLVKyBCKJmbkpVobvVKSvtz/WKdIPkaEoOrJo0aIFTE1NYWJiguDgYPzwww8Afhsx5OTkSOvp9Xqo1epq24mISFmyYbFnz54atdVEbm6u9PrgwYPo2LEjAECj0SAxMRElJSXIyspCZmYmevToge7duyMzMxNZWVkoKSlBYmIiNBrNU/VNRERPT/Y0VExMDIYOHSrb9nvTp0/HiRMnkJ+fj/79+2PKlCk4ceIELl68CABwdHREdHQ0AKBjx44YOnQoXn31VZiamiIqKgqmpqYAgKioKLzxxhsoLy9HYGCgFDBERKScasMiOTkZKSkp0Ov1mD9/vtR+9+5d6Yv8cZYsWfJIW3BwcLXrT5w4ERMnTnyk3dPTE56enrL9ERGR4VQbFmq1Gt26dcM333wDZ2dnqb1x48aIjIxUpDgiIqodqg2Lzp07o3PnzvDx8YG5ubmSNRERUS0je80iLS0NK1euxPXr11FWVgYhBFQqFZKSkpSoj4iIagHZsJg9ezYiIyPRrVs3mJhw3kEiovpINiysrKx4gZmIqJ6TDQs3Nzd8+OGH0Gq1sLCwkNofvuhNRER1m2xYnD17FgCQnp4utalUKnz++eeGq4qIiGoV2bD44osvlKiDiIhqMdmwWLlyZZXtb7/99jMvhoiIaifZsGjUqJH0uri4GIcPH0a7du0MWhQREdUusmExbty4Su/Hjx//yC/dERFR3fbED07cv3+/0rThRERU98mOLHS6//1oS0VFBW7duoXJkycbtCgiIqpdZMNizZo1/1vZzAy2trYwM1P0B/aIiMjIZE9DOTo64s6dOzh06BAOHDiAjIwMJeoiIqJaRDYsYmNj8X//93/Iy8tDXl4eZsyYwWcviIjqGdnzSVu3bsXmzZulW2jffPNNvPbaawgJCTF4cUREVDvU6G6oh38Zrya/kkdERHWL7MgiICAAwcHB8PLyAgAcPHgQgYGBBi+MiIhqD9mwCA0NRZ8+fXDq1CkAwKJFi9C1a1eDF0ZERLVHje6BbdWqFUxNTVFeXg4hBM6dO8cpyomI6hHZsFi2bBm2b9+ONm3aSG2copyIqH6RDYs9e/bgwIEDlX74iIiI6hfZu6GcnJxw584dJWohIqJaSnZkERYWBn9/fzg5OcHc3Fxqf3gaECIiqttkw2LWrFl488034eTkBBOTJ56kloiI6gDZsGjYsCHGjBmjRC1ERFRLyYaFq6srPv74Y2g0mkoXuXnrLBFR/SEbFufPnwcAnDlzRmrjrbNERPWLbFhUNcPszZs3DVIMERHVTjX+FaPbt29j37592LVrFy5fvozU1FRD1kVERLXIY8Pi119/RVJSEnbu3IkLFy6gqKgIq1atQu/evZWqj4iIaoFq74UNDw/H4MGDcfToUYSEhOCbb75B06ZN4ebmxltoiYjqmWq/9TMyMtC0aVO0b98e7du3h6mpKVQqlZK11SmirLhO9kVE9UO1p6ESEhJw+fJlJCYmYuzYsbCxsUFRURFu3ryJFi1aKFljnaAya4Cr0d0V6atN1A+K9ENE9cdjr1m0b98eU6dOxdSpU5Geno7ExEQEBQXB3t4eX331lVI1EhGRkdX44kO3bt0QERGBQ4cOITw8XHb9yMhIuLu7w8fHR2orKChAaGgotFotQkNDUVhYCAAQQmD+/Pnw8vKCTqfDuXPnpG22b98OrVYLrVaL7du3P8lnoyoUK3iKSsm+iMiwanzr7AMqlapGd0MFBARg9OjRiIiIkNpiYmLg7u6OsLAwxMTEICYmBjNmzEBKSgoyMzOxf/9+nD17FnPnzsWWLVtQUFCAlStXYtu2bVCpVAgICIBGo0GzZs2etGz6rwZmDdBvRT9F+jo65agi/RCR4RnstqbevXs/8qWelJQEf39/AIC/vz8OHjxYqV2lUsHFxQW3b99Gbm4uUlNT0a9fP1hbW6NZs2bo168fjhw5YqiSiYioGoreA5uXlwc7OzsAQMuWLZGXlwcA0Ov1sLe3l9azt7eHXq9/pF2tVkOv1ytZMhERoQZhsXr1aul1SUnJM+tYpVLxVlwioudEtWERExOD06dPY9++fVLba6+99oc6s7W1RW5uLgAgNzcXzZs3B/DbiCEnJ0daLycnB2q1+pF2vV4PtVr9h2ogIqInV21YtGvXDnv37kVWVhZGjhyJ999/HwUFBfj555+fujONRoP4+HgAQHx8PAYOHFipXQiBM2fOwMrKCnZ2dvDw8EBqaioKCwtRWFiI1NRUeHh4PHX/RET0dKq9G6pp06aYPn06Tpw4gS+++AKXL1/G0aNHsXbtWvzyyy+yz1k82DY/Px/9+/fHlClTEBYWhmnTpmHr1q1wcHDAsmXLAACenp5ITk6Gl5cXLC0tsXDhQgCAtbU1Jk2ahKCgIADA5MmTYW1t/aw+OxER1VC1YZGamopVq1bh6tWrWLRoETp16gRLS0ssWrSoRjtesmRJle2xsbGPtKlUKsyZM6fK9YOCgqSwICIi46j2NNT06dMRGxsLR0dH+Pn5oaKiArdu3cKIESMwYcIEJWskIiIjk30oz8PDA927d0f37t2xadMmbNq0Cbdu3VKiNiIiqiVkb52dOXOm9Hrx4sUAIN3FRERE9cMTPZTXuXNnQ9VBRES1GH/FiIiIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGQxLIiISBbDgoiIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGQxLIiISBbDgoiIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGQxLIiISBbDgoiIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGQxLIiISBbDgoiIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGQxLIiISBbDgoiIZDEsiIhIlpkxOtVoNGjcuDFMTExgamqKuLg4FBQU4N1338W1a9fg6OiIZcuWoVmzZhBCYMGCBUhOTkbDhg2xePFiODs7G6NsIqJ6y2gji9jYWCQkJCAuLg4AEBMTA3d3d+zfvx/u7u6IiYkBAKSkpCAzMxP79+/HvHnzMHfuXGOVTERUb9Wa01BJSUnw9/cHAPj7++PgwYOV2lUqFVxcXHD79m3k5uYas1QionrHaGExfvx4BAQE4OuvvwYA5OXlwc7ODgDQsmVL5OXlAQD0ej3s7e2l7ezt7aHX65UvmIioHjPKNYtNmzZBrVYjLy8PoaGhaNeuXaXlKpUKKpXKGKUREVEVjDKyUKvVAABbW1t4eXkhLS0Ntra20uml3NxcNG/eXFo3JydH2jYnJ0fanoiIlKF4WNy7dw93796VXh89ehQdO3aERqNBfHw8ACA+Ph4DBw4EAKldCIEzZ87AyspKOl1FRETKUPw0VF5eHiZPngwAKC8vh4+PD/r374/u3btj2rRp2Lp1KxwcHLBs2TIAgKenJ5KTk+Hl5QVLS0ssXLhQ6ZKJiOo9xcOidevW2LFjxyPtNjY2iI2NfaRdpVJhzpw5SpRGRETVqDW3zhIRUe3FsCAiIlkMCyIiksWwICIiWQwLIiKSxbAgIiJZDAsiIpLFsCAiIlkMCyIiksWwICIiWQwLIiKSxbAgIiJZDAsiIpLFsCAiIlkMCyIiksWwICIiWQwLIiKSxbAgIiJZDAsiIpLFsCAiIlkMCyIiksWwICIiWQwLIiKSxbAgIiJZDAsiIpLFsCAiVJSV16l+6NkzM3YBRGR8JmamuLDgG4P302W2xuB9kGFwZEFkRGUlJXWyL6p7OLIgMiIzCwssGB2kSF+z/71VkX6obuLIgoiIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGQxLIiISBbDgoiIZDEsiIgeUl5eXKf6eVaem4fyUlJSsGDBAlRUVCA4OBhhYWHGLomI6iBT0wbYvKWPwfv5a/AJg/fxLD0XI4vy8nJER0dj3bp1SExMxK5du5CRkWHssojoGSotLa2TfT2N4nLlJlysaV/PxcgiLS0Nbdu2RevWrQEA3t7eSEpKQocOHYxcGRE9K+bm5pg7d64ifSnVz9NqYGqKF7fuU6Svs0GDa7SeSgghDFzLH7Z3714cOXIECxYsAADEx8cjLS0NUVFRVa7v5uYGR0dHJUskInruXbt2DcePH69y2XMxsnhS1X1YIiJ6Os/FNQu1Wo2cnBzpvV6vh1qtNmJFRET1y3MRFt27d0dmZiaysrJQUlKCxMREaDT8ERUiIqU8F6ehzMzMEBUVhTfeeAPl5eUIDAxEx44djV0WEVG98Vxc4CYiIuN6Lk5DERGRcTEsiIhIFsOCiJ5IdnY2fHx8jF0GKYxhQURUhwghUFFR8cz3y7D4nezsbAwdOhTvv/8+vL29MW7cOPz666+K1vDJJ5/gX//6l/R+6dKliI2NVbSGdevW4fPPPwcALFy4EGPGjAEAHDt2DOHh4YrWsmrVKgwePBgjRozA9OnTsX79ekX7fyAhIQFBQUHw8/NDVFQUyhWcvwf47W9zyJAhCA8Px9ChQzF16lTcv39f0RoeKCsrM2odvx/drF+/HitWrFC0hgfi4+Oh0+ng6+uLGTNmGKWG7OxsDB48GDNnzoSPjw9u3LjxzPtgWFThypUrGDVqFBITE2FlZYV9+5SZo+WBwMBAJCQkAAAqKiqQmJgIX19fRWtwdXXFyZMnAQDp6em4d+8eSktLcerUKfTu3VuxOtLS0rB//37s2LEDa9euRXp6umJ9P+zy5cvYs2cPNm3ahISEBJiYmGDnzp2K1/HLL79g5MiR2LNnDxo3bowvv/xS8RpqUx3GdunSJfzzn/9EbGwsduzYgdmzZxutlitXrmDkyJFITEw0yHRHDIsqtGrVCl26dAEAODs749q1a4r3b21tjfPnzyM1NRVdu3aFjY2NojU4Ozvj3LlzuHv3LiwsLODi4oL09HScPHkSrq6uitXx/fffY+DAgWjQoAGaNGmCAQMGKNb3w44dO4b09HRpZHHs2DFkZWUpXscLL7yAl156CQDg6+uLU6dOKV5DbarD2L777jsMGTIEzZs3BwBYW1sbrRYHBwe4uLgYbP/PxUN5SrOwsJBem5qaorhY+R8pCQ4ORlxcHG7evInAwEDF+zc3N0erVq0QFxeHnj17olOnTjh+/DiuXr2K9u3bK16PsQkhMGzYMMVPwf2eSqV67Pv6UoeZmVml8/LG+G+0tmnUqJFB98+RRS01aNAgHDlyBD/88AM8PDyMUoOrqys+++wz9O7dG66urvjqq6/QpUsXRb8YevXqhUOHDqG4uBhFRUU4fPiwYn0/zN3dHfv27UNeXh4AoKCgQPERJwBcv34dp0+fBgDs2rVL+r/7+laHra0t8vLykJ+fj5KSEqP9Xbz88svYu3cv8vPzAfz2d1FXcWRRS1lYWMDNzQ1NmzaFqampUWpwdXXFmjVr4OLigkaNGqFBgwaKnoICgB49ekCj0cDX1xe2trZwcnKClZWVojUAQIcOHTBt2jSMGzcOFRUVMDc3R1RUlOJT4f/5z3/Gxo0b8d5776FDhw4YMWKEov3XljrMzc0xefJkBAcHQ61Wo127dor2/0DHjh0xYcIEhISEwMTEBF27dsXixYuNUouhcbqPWqqiogLDhg3DJ598gj/96U/GLseoioqK0LhxY9y/fx+jRo3CvHnz4OzsbOyyFJednY0JEyZg165dxi6F6iGOLGqhjIwMvPXWW/Dy8qr3QQEAUVFRyMjIQHFxMYYNG1Yvg4LI2DiyICIiWbzATUREshgWREQki2FBRESyeIGb6oT8/HyMHTsWAHDz5k2YmJhIT9Vu2bKl0oOWhvLhhx8iJSUF/fv3R0REhMH7exJxcXFIT09HVFSUsUuh5xTDguoEGxsbaT6tFStWoFGjRhg/fryiNWzevBknTpww2nMxz1JZWRnMzPj1QP/D01BUJ/3666/QaDQoLS0FANy9e1d6HxISgvnz58PPzw8+Pj5IS0sDANy7dw+RkZEICgqCv78/Dh48+Mh+hRD48MMP4ePjA51Oh927dwMAJkyYgHv37iEgIEBqe6CoqAiRkZHQ6XTQ6XTSxJRz5sxBQEAAvL29sXz5cml9jUaDjz76CDqdDkFBQbhy5UqVnzElJQXDhg2Dr68vXn/9dQC/PUE8adIk6HQ6/PWvf8XFixcf2S47OxtjxoyBTqfD66+/juvXrwMAZs2ahaioKAQHB+Pvf//7Ex1vqgcEUR2zfPlysW7dOjFr1ixx4MABIYQQX331lVi0aJEQQojRo0eL2bNnCyGEOHHihPD29hZCCPHxxx+L+Ph4IYQQhYWFQqvViqKiokr73rt3rxg7dqwoKysT//nPf4Snp6fQ6/VCCCFcXFyqrOejjz4S8+fPl94XFBQIIYTIz88XQghRVlYmRo8eLS5cuCCEEGLAgAFi9erVQgghtm/fLsLCwh7ZZ15enujfv7+4evVqpX1FR0eLFStWCCGE+Pbbb4Wvr68QQoht27aJDz74QAghxFtvvSXi4uKEEEJs2bJFTJw4UQghREREhAgLCxNlZWWPObpUX3FkQXVWUFAQtm3bBuC3c/YBAQHSMm9vbwBA7969cffuXdy+fRupqalYu3Yt/Pz8EBISguLi4hDSnLsAAAKtSURBVEd+F+DUqVPw9vaGqakpWrRogd69e+OHH354bB3Hjh3DqFGjpPfNmjUDAOzZswfDhg2Dv78/Ll26hMuXL0vrPPitBm9vb5w5c+aRfZ45cwaurq5o3bo1gP/Ndnrq1Cn4+fkB+G0+q4KCAty9e7fStqdPn5b27+fnV2nG2CFDhtSJ02j07PGkJNVZL730Ej744AMcP34c5eXlcHJykpZVN2vq8uXLFZlnKCsrC5999hm2bt2KZs2aYdasWY+dObW8vFwKO41Gg+7duxukLktLS4Psl55/HFlQnebv74/w8PBKowoA0nWFkydPwsrKClZWVvDw8MC///1viP9OanD+/PlH9ufq6oo9e/agvLwct27dwsmTJ9GjR4/H1tC3b19s3LhRel9YWIiioiJYWlrCysoKN2/eREpKSqVt9uzZI9XZs2dPmJqaIiEhAQkJCXjnnXfg4uKCkydPSr+p8WC2U1dXV+zYsQMAcPz4cdjY2KBJkyaV9t2zZ08kJiYCAHbu3Kn45JD0fOLIguo0nU6HZcuWVfoJTgBo0KAB/P39UVZWhoULFwIAJk2ahIULF8LX1xcVFRVo1aoVPv3000rbeXl54fTp0/Dz84NKpcKMGTPQsmXLx9YwceJEREdHw8fHByYmJnj77beh1WrRtWtXDB06FPb29ujVq1elbQoLC6HT6WBhYYElS5Y8ss/mzZsjOjoaU6ZMQUVFBWxtbbFhwwa8/fbbeO+996DT6WBpaVnlDKh/+9vfEBkZifXr16N58+ZYtGhRjY4l1W+cG4rqtL179yIpKanS3T0hISGYOXOmwU7l/FEajQZbt26VnhMhqg04sqA6a968eUhJSUFMTIyxSyF67nFkQUREsniBm4iIZDEsiIhIFsOCiIhkMSyIiEgWw4KIiGT9P7wMtsqWdPzNAAAAAElFTkSuQmCC\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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n5ub667IIiLSCrd9KM/f35/s7Gw6d+7MuXPnePDBBxk/fjwATz75JBMmTGixfklJCRUVFWzdupVPPvmEJUuWsH79eurq6lizZg3vvPMOFouF5ORkbDYbgYGB7oouIiL/xG1HFhaLhc6dOwPQ3NxMc3PzZR/PWFRURFJSEhaLhREjRtDQ0EBNTQ1lZWWMGzeOoKAgAgMDGTduHKWlpe6KLW3Q+bNn29W8Im2RW2/34XQ6SU5O5vPPP+fBBx/krrvu4q233mLlypW88sorREZG8tOf/hR/f38cDgchISGubUNCQnA4HF8bt1qtLR4mLzc/n1tuoXh8tMfnjS4p9vicIm2VWy9w+/r6kp+fT3FxMeXl5Xz66afMnz+fzZs3884771BfX09WVpY7I4iIyHXgkXdDdevWjTFjxlBaWkpwcDAWiwV/f3+Sk5NdD4+3Wq1UV1e7tqmursZqtX5t3OFwYLVaPRFbRET+xm1lcfz4cRoaGgA4c+YM27dvJywsjJqaGuDCu58KCwsJDw8HwGazkZeXh2EY7N27l65duxIcHExUVBRlZWXU19dTX19PWVkZUVFR7ootIiKX4LZrFjU1NWRkZOB0OjEMgwkTJnDPPffw0EMPceLECQzD4M477+SZZ54BIDo6muLiYux2OwEBAaxYsQKAoKAgZs+eTWpqKgBz5swhKCjIXbFFROQSLIZhGN4Ocb0lJyfreRY3GV3gFnG/y/3t1Ce4RUTElMpCRERMqSxERMSUykJEREypLERExJTKQkRETKksRETElMpCRERMqSxERMSUykJEREypLERExJTKQkRETKksRETElMpCRERMqSxERMSUykJEREypLERExJTKQkRETKksRETElMpCRERMqSxERMSU28ri7NmzpKamMnnyZOLi4li1ahUAlZWVTJ06Fbvdzrx582hqagKgqamJefPmYbfbmTp1KlVVVa59vfbaa9jtdmJjYyktLXVXZBERaYXbysLf35/s7Gzeffdd8vLyKC0tZe/evbz00ktMnz6dbdu20a1bNzZs2ADA+vXr6datG9u2bWP69Om89NJLABw+fJiCggIKCgpYu3YtzzzzDE6n012xRUTkEtxWFhaLhc6dOwPQ3NxMc3MzFouFP/zhD8TGxgIwZcoUioqKAPjggw+YMmUKALGxsXz88ccYhkFRURFxcXH4+/vTt29f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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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8vKqnbtFixa0bduWM2fOsHLlymvuLzcvlYU0Crfddht9+/atcdtDDz1E8+bNGTp0KPPmzSMqKsq5raysjN///vcMHjyYkSNH4u3tzfTp028ow9ChQ/nd737H448/zvDhw8nLy3N+qa99+/asWrWKt956iyFDhrB69WpWrVpFhw4dbuixrse8efPYunUrAwcO5F//9V9rLIJRo0YRGxtLTEwM99xzD3FxcTWe67HHHiMpKYmgoCC2bdvGQw89RHl5OXfddReTJk3i7rvvrrb/1KlT2blzJ4MGDWLx4sV18vykYbAZzSdFRMSCZhYiImJJZSEiIpZUFiIiYkllISIilm7KL+UNGTIEPz8/V8cQEWlUTp06xcGDB2vcdlOWhZ+fH6mpqa6OISLSqMTGxl51m96GEhERSyoLERGxpLIQERFLKgsREbGkshAREUsqCxERsaSyEBERSyoLERGxpLIQERFLKguRWrhcXu7qCDVqqLnk5nNTLvch8nNr1qIFmSNCXB3jCiFZma6OIE2EZhYiImJJZSEiIpZUFiIiYkllISIiluqsLMrLy4mLi2PcuHFERkby8ssvA5CXl8fEiRMJCwtjzpw5VFRUAFBRUcGcOXMICwtj4sSJ5OfnO8/12muvERYWRkREBHv37q2ryCIichV1VhYeHh6sWbOG999/n7S0NPbu3cuxY8f44x//yLRp09i9ezdt27Zl48aNALz33nu0bduW3bt3M23aNP74xz8CkJOTQ3p6Ounp6axevZrnnnuOqqqquootIiI1qLOysNlstG7dGoDKykoqKyux2Wz853/+JxEREQCMHz+ejIwMAP72t78xfvx4ACIiIvj73/+OMYaMjAwiIyPx8PCga9eudOvWjezs7LqKLSIiNajTaxZVVVVER0czdOhQhg4dSteuXWnbti3u7t9/vcPX1xeHwwGAw+Hg1ltvBcDd3R0vLy9KSkpwOBz4+vo6z2m3253HiIhI/ajTsnBzc2Pz5s1kZmaSnZ3Nl19+WZcPJyIidaRePg3Vtm1bhgwZwrFjxzh79iyVlZUAFBQUYLfbge9nDKdPnwa+f9vq3LlztG/fHrvdTkFBgfNcDofDeYyIiNSPOiuL4uJizp49C8B3333HgQMH6N69O0OGDGHnzp0AbNq0idDQUABCQ0PZtGkTADt37uSuu+7CZrMRGhpKeno6FRUV5OXlkZubS79+/eoqtoiI1KDO1oYqLCwkKSmJqqoqjDHcd999jBw5kh49evDEE0+wfPlybr/9diZOnAhAXFwcTz75JGFhYbRr146XXnoJgJ49ezJ69GjGjBmDm5sbCxcuxM3Nra5ii4hIDWzGGOPqED+32NhYUlNTXR1DbjJaSFBudtf626lvcIuIiCWVhYiIWFJZiIiIJZWFiIhYUlmIiIgllYWIiFhSWYiIiCWVhYiIWFJZiIiIJZWFiIhYUlmIiIgllYWIiFhSWYiIiCWVhYiIWFJZiIiIJZWFiIhYUlmIiIgllYWIiFhSWYiIiCWVhYiIWFJZiIiIJZWFiIhYUlmIiIgllYWIiFiqs7I4ffo08fHxjBkzhsjISNasWQPAihUruPvuu4mOjiY6OprMzEznMa+99hphYWFERESwd+9e53hWVhYRERGEhYWRkpJSV5FFROQq3OvqxG5ubiQlJREYGMj58+eZMGECw4YNA2DatGlMnz692v45OTmkp6eTnp6Ow+EgISGBnTt3ApCcnMxbb72F3W4nLi6O0NBQevToUVfRRUTkn9RZWfj4+ODj4wNAmzZt8Pf3x+FwXHX/jIwMIiMj8fDwoGvXrnTr1o3s7GwAunXrRteuXQGIjIwkIyNDZSEiUo/q5ZpFfn4+J06c4I477gBg3bp1REVFMX/+fEpLSwFwOBz4+vo6j7Hb7TgcjquOi4hI/anzsigrK2P27Nk8/fTTtGnThsmTJ7N79242b96Mj48PL7zwQl1HEBGRn6hOy+LSpUvMnj2bqKgowsPDAejUqRNubm40a9aMiRMn8sknnwDfzxgKCgqcxzocDux2+1XHRUSk/tRZWRhjWLBgAf7+/iQkJDjHCwsLnbf37NlDz549AQgNDSU9PZ2Kigry8vLIzc2lX79+9O3bl9zcXPLy8qioqCA9PZ3Q0NC6ii0iIjWoswvcR44cYfPmzQQEBBAdHQ1AYmIiW7du5fPPPwfAz8+P5ORkAHr27Mno0aMZM2YMbm5uLFy4EDc3NwAWLlzIww8/TFVVFRMmTHAWjIiI1A+bMca4OsTPLTY2ltTUVFfHkJtM5ogQV0e4QkhWpvVOIrV0rb+d+ga3iIhYUlmIiIgllYWIiFhSWYiIiCWVhYiIWFJZiIiIJZWFiIhYUlmIiIgllYWIiFhSWYiIiCWVhYiIWFJZiIiIJZWFiIhYUlmIiIgllYWIiFhSWYiIiCWVhYiIWFJZNBKmstzVEWrUUHOJyM+rzn6DW35eNvcWfJ3c19UxrnDbwk9cHUFE6oFmFiIiYkllISIillQWIiJiSWUhIiKWVBYiImKpzsri9OnTxMfHM2bMGCIjI1mzZg0AZ86cISEhgfDwcBISEigtLQXAGMPixYsJCwsjKiqKTz/91HmuTZs2ER4eTnh4OJs2baqryCIichV1VhZubm4kJSWxbds23n33Xf7yl7+Qk5NDSkoKwcHB7Nq1i+DgYFJSUgDIysoiNzeXXbt2sWjRIp599lng+3JZuXIlGzZs4L333mPlypXOghERkfphWRbbt2+v1dg/8/HxITAwEIA2bdrg7++Pw+EgIyODmJgYAGJiYtizZw+Ac9xms9G/f3/Onj1LYWEh+/btY9iwYXh7e9OuXTuGDRvG3r17r+tJiojIT2NZFj/8y99q7Fry8/M5ceIEd9xxB0VFRfj4+ABwyy23UFRUBIDD4cDX19d5jK+vLw6H44pxu92Ow+G4rscXEZGf5qrf4M7MzCQrKwuHw8HixYud4+fPn8fNza3WD1BWVsbs2bN5+umnadOmTbVtNpsNm812A7FFRKQ+XXVmYbfb6dOnDy1atCAwMND5v9DQUN54441anfzSpUvMnj2bqKgowsPDAejYsSOFhYUAFBYW0qFDB+fjFRQUOI8tKCjAbrdfMe5wOLDb7df/TEVE5IZddWbRu3dvevfuzdixY2nevPl1n9gYw4IFC/D39ychIcE5HhoaSlpaGjNmzCAtLY1Ro0Y5x//85z8TGRnJxx9/jJeXFz4+PgwfPpwXX3zReVF73759JCYmXnceERG5cZYLCWZnZ7Ny5Uq++eYbKisrMcZgs9nIyMi45nFHjhxh8+bNBAQEEB0dDUBiYiIzZsxgzpw5bNy4kc6dO7N8+XIAQkJCyMzMJCwsDE9PT5YsWQKAt7c3jz76KHFxcQDMmjULb2/vn/SkRUTk+liWxYIFC5g/fz59+vShWbPaf9I2KCiIf/zjHzVu++E7Fz9ms9l45plnatw/Li7OWRYiIlL/LMvCy8uLkJCQ+sgiIiINlGVZDBkyhGXLlhEeHo6Hh4dz/IfvUIiIyM3Psiw+/vhjAI4fP+4cs9lsrF27tu5SiYhIg2JZFm+//XZ95BARkQbMsixWrlxZ4/hjjz32s4cREZGGybIsWrVq5bxdXl7Ohx9+iL+/f52GEhGRhsWyLH79619Xuz99+nSmT59eZ4FERKThue4lyi9evFht+Q0REbn5Wc4soqKinLcvX75McXExs2bNqtNQIiLSsFiWxapVq/7/zu7udOzYEXd3y8NEROQmYvk2lJ+fH+fOneODDz5g9+7d5OTk1EcuERFpQCzLYs2aNfzLv/wLRUVFFBUV8eSTT+q7FyIiTYzl+0kbN25kw4YNzo/QPvLII0yaNIn4+Pg6DyciIg1DrT4N9eNfxrueX8kTEZGbg+XMIjY2lokTJxIWFgbAnj17mDBhQp0HExGRhsOyLBISEhg8eDBHjhwBYOnSpfzyl7+s82AiItJw1OozsF26dMHNzY2qqiqMMXz66adaolxEpAmxLIvly5ezadMmbrvtNueYligXEWlaLMti+/bt7N69u9oPH4mISNNi+WmogIAAzp07Vx9ZRESkgbKcWcyYMYOYmBgCAgJo3ry5c/zHy4CIiMjNzbIskpKSeOSRRwgICKBZs+tepFZERG4ClmXRsmVLpk6dWh9ZRESkgbIsi6CgIP70pz8RGhpa7SK3PjorItJ0WJbFZ599BsCxY8ecY7X56Oz8+fP58MMP6dixI1u3bgVgxYoVbNiwgQ4dOgCQmJhISEgIAK+99hobN26kWbNm/P73v+fuu+8GICsri+eff57Lly8zceJEZsyYcQNPU0REfgrLsqhphdlvv/3W8sSxsbFMmTKFefPmVRufNm3aFT/LmpOTQ3p6Ounp6TgcDhISEti5cycAycnJvPXWW9jtduLi4ggNDaVHjx6Wjy8iIj+fWv+K0dmzZ9m5cydbt27liy++YN++fdfcf9CgQeTn59fq3BkZGURGRuLh4UHXrl3p1q0b2dnZAHTr1o2uXbsCEBkZSUZGhspCRKSeXbMsvvvuOzIyMtiyZQsnTpygrKyMV155hUGDBt3wA65bt460tDT69OlDUlIS7dq1w+FwcMcddzj3sdvtOBwOAHx9fauN/1AiIiJSf676Wdi5c+cSERHB/v37iY+P529/+xtt27ZlyJAhN/wR2smTJ7N79242b96Mj48PL7zwwg0HFxGR+nPVv/o5OTm0bduW7t270717d9zc3LDZbD/pwTp16oSbmxvNmjVj4sSJfPLJJ8D3M4aCggLnfg6HA7vdftVxERGpX1cti82bN7N8+XLKysqYNm0akydPpqysrFYXt6+msLDQeXvPnj307NkTgNDQUNLT06moqCAvL4/c3Fz69etH3759yc3NJS8vj4qKCtLT0wkNDb3hxxcRkRtzzWsW3bt3Z/bs2cyePZvjx4+Tnp5OXFwcvr6+vPPOO9c8cWJiIocOHaKkpIQRI0bw+OOPc+jQIT7//HMA/Pz8SE5OBqBnz56MHj2aMWPG4ObmxsKFC52/yLdw4UIefvhhqqqqmDBhgrNgRESk/tiMMeZ6DjDGcPjw4Z90kbuuxcbGkpqa6uoYP7uvk/u6OsIVblv4iasj1JvMESGujnCFkKxMV0eQm8i1/nZe95Vqm83WoItCRER+floZUERELKksRETEkmVZ/Pu//7vzdkVFRZ2GERGRhumqZZGSksJHH33kXKMJYNKkSfUSSkREGparfnTW39+fHTt2kJeXxwMPPIC/vz9nzpzhyy+/xN/fvz4zioiIi111ZtG2bVsSExPp1q0bb7/9tvMHkF5//XXuv//+egsoIiKud9WZxb59+3jllVf4+uuvWbp0Kb169cLT05OlS5fWZz4REWkArjqzSExMZM2aNfj5+REdHc3ly5cpLi5m8uTJzJw5sz4zioiIi1n+nsXw4cPp27cvffv2Zf369axfv57i4uL6yCYiIg2E5Udnn3rqKeftH5YU/+FnUUVEpGm4ri/l9e7du65yiIhIA6ZvcIuIiCWVhYiIWFJZiIiIJZWFiIhYUlmIiIgllYWIiFhSWYg0AZWXqlwdoUYNNZdcyfIb3CLS+Lk3d2Pl3C2ujnGFx/4U5eoIUkuaWYiIiCWVhYiIWFJZiIiIJZWFiIhYqrOymD9/PsHBwYwdO9Y5dubMGRISEggPDychIYHS0lIAjDEsXryYsLAwoqKi+PTTT53HbNq0ifDwcMLDw9m0aVNdxRURkWuos7KIjY1l9erV1cZSUlIIDg5m165dBAcHk5KSAkBWVha5ubns2rWLRYsW8eyzzwLfl8vKlSvZsGED7733HitXrnQWjIiI1J86K4tBgwbRrl27amMZGRnExMQAEBMTw549e6qN22w2+vfvz9mzZyksLGTfvn0MGzYMb29v2rVrx7Bhw9i7d29dRRYRkauo12sWRUVF+Pj4AHDLLbdQVFQEgMPhwNfX17mfr68vDofjinG73Y7D4ajPyCIiggsvcNtsNmw2m6seXkRErkO9lkXHjh0pLCwEoLCw0PnzrHa7nYKCAud+BQUF2O32K8YdDgd2u70+I4uICPVcFqGhoaSlpQGQlpbGqFGjqo0bYzh27BheXl74+PgwfPhw9u3bR2lpKaWlpezbt4/hw4fXZ2QREaEO14ZKTEzk0KFDlJSUMGLECB5//HFmzJjBnDlz2LhxI507d2b58uUAhISEkJmZSVhYGJ6enixZsgQAb29vHn30UeLi4gCYNWsW3t7edRVZRESuos7K4sUXX6xxfM2aNVeM2Ww2nnnmmRr3j4uLc5aFiIi4hr7BLSIillQWIiJiSWUhIiKWVBYiImJJZSEiIpZUFiIidehyZcP8nfHrzaXf4BYRqUPN3N048fzfXB3jCrcvCL2u/TWzEBERSyoLERGxpLIQERFLKgsREbGkshAREUsqCxERsaSyEBERSyoLERGxpLIQERFLKgsREbGkshAREUsqCxERsaSyEBERSyoLERGxpLIQERFLKgsREbGkshAREUsu+aW80NBQWrduTbNmzXBzcyM1NZUzZ87wxBNPcOrUKfz8/Fi+fDnt2rXDGMPzzz9PZmYmLVu25IUXXiAwMNAVsUVEmiyXzSzWrFnD5s2bSU1NBSAlJYXg4GB27dpFcHAwKSkpAGRlZZGbm8uuXbtYtGgRzz77rKsii4g0WQ3mbaiMjAxiYmIAiImJYc+ePdXGbTYb/fv35+zZsxQWFroyqohIk+Oyspg+fTqxsbG8++67ABQVFeHj4wPALbfcQlFREQAOhwNfX1/ncb6+vjgcjvoPLCLShLnkmsX69eux2+0UFRWRkJCAv79/te02mw2bzeaKaCIiUgOXzCzsdjsAHTt2JCwsjOzsbDp27Oh8e6mwsJAOHTo49y0oKHAeW1BQ4DxeRETqR72XxYULFzh//rzz9v79++nZsyehoaGkpaUBkJaWxqhRowCc48YYjh07hpeXl/PtKhERqR/1/jZUUVERs2bNAqCqqoqxY8cyYsQI+vbty5w5c9i4cSOdO3dm+fLlAISEhJCZmUlYWBienp4sWbKkviOLiDR59V4WXbt25f33379ivH379qxZs+aKcZvNxjPPPFMf0URE5CoazEdnRUSk4VJZiIiIJZWFiIhYajJlUX6pytURatRQc4mI/JhLvpTnCi2au3Hnk2tdHeMKR/4w1dURREQsNZmZhYiI3DiVhYiIWFJZiIiIJZWF1IvyynJXR6hRQ80l0tA0mQvc4lot3FswbMUwV8e4wv7H97s6gkijoJmFiIhYUlmIiIgllYWIiFhSWYiIiCWVhYg0aJUVFa6OcFUNOdvPTZ+GEpEGzd3Dg+enxLk6Ro0W/HmjqyPUG80sRETEkspCREQsqSxERMSSykJERCypLERExJLKQkRELKksRETEkspCREQsNZqyyMrKIiIigrCwMFJSUlwdR0SkSWkUZVFVVUVycjKrV68mPT2drVu3kpOT4+pYIiJNRqMoi+zsbLp160bXrl3x8PAgMjKSjIwMV8cSEWkybMYY4+oQVnbs2MHevXt5/vnnAUhLSyM7O5uFCxfWuP+QIUPw8/Orz4giIo3eqVOnOHjwYI3bbsqFBK/2ZEVE5MY0ireh7HY7BQUFzvsOhwO73e7CRCIiTUujKIu+ffuSm5tLXl4eFRUVpKenExoa6upYIiJNRqN4G8rd3Z2FCxfy8MMPU1VVxYQJE+jZs6erY4mINBmN4gK3iIi4VqN4G0pERFxLZSEiIpZUFiIusGLFCt544w1Xx5BGZsCAAS57bJWFiIhYUllch/z8fMaOHeu8/8Ybb7BixQoXJrp+r7zyChEREUyePJnExMRG8a/b/Px87rvvPpKSkoiIiGDu3LkcOHCA+++/n/DwcLKzs10dsVZeffVV52t/8uRJV8e5Lj/8N5g7dy6jR49m9uzZXLx40dWxamX16tWsXbsWgCVLljB16lQA/v73vzN37lxXRmtUVBZNSHZ2Nrt27eL999/n9ddf5/jx466OVGtff/01CQkJbN++nZMnT7JlyxbWr1/PU089xapVq1wdz9Lx48fZtm0baWlpvP7663zyySeujnTdTp48yQMPPMD27dtp3bo1f/nLX1wdqVaCgoI4fPgw8P1/hwsXLnDp0iWOHDnCoEGDXJyu8VBZNCFHjx5l1KhRtGjRgjZt2jBy5EhXR6q1Ll260KtXL5o1a0aPHj0IDg7GZrPRq1cvTp065ep4lg4fPsy9996Lp6cnbdq0aZRfKr311lu58847ARg3bhxHjhxxcaLaCQwM5NNPP+X8+fN4eHjQv39/jh8/zuHDhwkKCnJ1vEajUXwpr6Fwd3fn8uXLzvvl5eUuTNO0eHh4OG83a9bMed9ms1FVVeWqWE2KzWa75v2Gqnnz5nTp0oXU1FQGDBhAr169OHjwIF9//TXdu3d3dbxGQzOL69CxY0eKioooKSmhoqKCDz/80NWRrsvAgQP54IMPKC8vp6ysrNHlb8wGDRrEnj17+O677zh//jwffPCBqyNdt2+++YaPPvoIgK1btzpnGY1BUFAQb775JoMGDSIoKIh33nmH22+/vdEUXkOgmcV1aN68ObNmzWLixInY7Xb8/f1dHem69OvXj9DQUMaNG0fHjh0JCAjAy8vL1bGahMDAQMaMGUN0dDQdOnSgb9++ro503X7xi1+wbt06nn76aXr06MHkyZNdHanWgoKCWLVqFUJ+/w4AAATeSURBVP3796dVq1a0aNFCb0FdJy330cSUlZXRunVrLl68yIMPPsiiRYsIDAx0dSxp4PLz85k5cyZbt251dRRxEc0smpiFCxeSk5NDeXk548ePV1GISK1oZiEiIpZ0gVtERCypLERExJLKQkRELKks5KZWUlJCdHQ00dHRDBs2jLvvvtt5v6Kiol4yLFu2jMjISJYtW1Zt/EZWnr3aqqP/9m//xoEDBwD4j//4j1qt21Tb/URAn4aSm1z79u3ZvHkz8P0f51atWjF9+vR6zbBhwwYOHTqEm5tbnT3G7373O+fttWvXMm7cODw9Pa95TG33EwHNLKSJ+e677wgNDeXSpUsAnD9/3nk/Pj6exYsXEx0dzdixY52r2V64cIH58+cTFxdHTEwMe/bsueK8xhiWLVvG2LFjiYqKYtu2bQDMnDmTCxcuEBsb6xz7sZycHOLj4xk1apRzZVSARx99lNjYWCIjI3n33XerHbNkyRIiIyN56KGHKC4uBiApKYkdO3awdu1aCgsLeeihh4iPjwfgmWeecZ7r5ZdfBqhxP5FrMiJNxMsvv2xWr15tkpKSzO7du40xxrzzzjtm6dKlxhhjpkyZYhYsWGCMMebQoUMmMjLSGGPMn/70J5OWlmaMMaa0tNSEh4ebsrKyaufesWOHmTZtmqmsrDT/+7//a0JCQozD4TDGGNO/f/+r5pk0aZIpLy83RUVFZvDgwaaiosIYY0xJSYkxxpiLFy+ayMhIU1xcbIwxJiAgwGzevNkYY8yKFSvMc889Z4wxZt68eWb79u3GGGNGjhxpioqKnI/zw7kqKyvNlClTzIkTJ2rcT+RaNLOQJicuLo6//vWvAKSmphIbG+vcFhkZCXy/ltP58+c5e/Ys+/bt4/XXXyc6Opr4+HjKy8s5ffp0tXMeOXKEyMhI3Nzc6NSpE4MGDarVMuQhISF4eHjQoUMHOnToQFFREQBvv/0248aN41e/+hWnT5/mq6++Ar5fRHHMmDEAREdH12rl1+3btzN+/HhiYmL4n//5H7744otavEoi1emahTQ5d955J8899xwHDx6kqqqKgIAA57arraz68ssv18laYD9eTdfNzY3KykoOHjzIgQMHePfdd/H09HQWVE2sFsLLy8vjzTffZOPGjbRr146kpCStliw3RDMLaZJiYmKYO3dutVkF4LyucPjwYby8vPDy8mL48OH8+c9/xvzfYgefffbZFecLCgpi+/btVFVVUVxczOHDh+nXr98NZTt37hzt2rXD09OTL774gmPHjjm3Xb58mZ07dwKwZcuWGld+bd26NWVlZcD3a4F5enri5eXFt99+S1ZWVo37iVjRzEKapKioKJYvX17tZ3IBWrRoQUxMDJWVlSxZsgT4/mLzkiVLGDduHJcvX6ZLly689tpr1Y4LCwvjo48+Ijo6GpvNxpNPPsktt9xyQ9lGjBjBO++8w+jRo/nFL35B//79ndtatWpFdnY2r776Kh06dGD58uVXHP+rX/2Khx9+GB8fH95++21++ctfMnr0aHx9fRk4cOBV9xO5Fq0NJU3Sjh07yMjI4A9/+INzLD4+nqeeeqpRLh8uUtc0s5AmZ9GiRWRlZZGSkuLqKCKNhmYWIiJiSRe4RUTEkspCREQsqSxERMSSykJERCypLERExNL/A5hWUKWihYfEAAAAAElFTkSuQmCC\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "iVBORw0KGgoAAAANSUhEUgAAAYsAAAEWCAYAAACXGLsWAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADh0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uMy4yLjIsIGh0dHA6Ly9tYXRwbG90bGliLm9yZy+WH4yJAAAgAElEQVR4nO3de1zU9YL/8dc4pJkieIkhTd3FsFrveeEoKNvUgAo4CPgoLVvNDscumkez9LjHisxsu/kwd4+xduG0ra2aQEpe6QRip1xLxdTqWFFgMhQqeEm5+Pn90TY/SfCrHodBfT//mvl8L/P+Tj14+73M92szxhhERETOopm/A4iISNOnshAREUsqCxERsaSyEBERSyoLERGxpLIQERFLKgu5rM2aNYuXXnrJ3zEa1csvv8wjjzwCQElJCTfeeCM1NTV+TiWXOpWF+JXT6aRnz54cPHiwznhiYiI33ngjJSUlfkrWdIwfP54VK1b4O8Z5O7205NKnshC/69SpEzk5Od73X3zxBT/99FOj59C/vusyxnDq1Cl/x5AmQmUhfud2u8nKyvK+z8rKIjExsc48v/7X9apVqxg7dizw8x+1+fPnM3jwYG655RYSEhL48ssvvfNWVlaSmppKv379GDNmDN9995132o033shbb71FTEwMMTExACxfvhyXy8WgQYOYPHkyHo/HO/+nn35KcnIy/fv3Jzk5mU8//bROxpdeeok777yTfv36MXnyZA4dOsSMGTO45ZZbSE5ObnBP6eTJkzzyyCNEREQwYMAAkpOT+fHHH3nppZfYtm0baWlp9OvXj7S0NADmzZtHdHQ0t9xyC0lJSWzbtu2cvuv169fjdDrrfD+//p5/2YY+ffpQXFx81m32eDxMnjyZQYMG4XK5WL58OQD5+fm88sorrF27ln79+jFq1KhzyidNmBHxo1tvvdVs2bLFxMTEmH379pmamhozdOhQU1JSYrp3726Ki4uNMcbcfffdZvny5d7l3nnnHXPnnXcaY4zJz883o0ePNhUVFebUqVNm3759xuPxGGOMeeyxx8ygQYPMzp07TXV1tZk+fbqZNm2adz3du3c3EyZMMIcOHTI//fST+fDDD82gQYPMZ599Zk6ePGnS0tLMuHHjjDHGHDp0yAwYMMBkZmaa6upqs3r1ajNgwABz8OBBb8bbb7/dfPvtt6aystKMGDHCxMTEmC1btpjq6mozc+ZMM2vWrHq/h2XLlpnf/e535vjx46ampsbs2rXLHDlypN5tN8aYrKwsc/DgQVNdXW1effVVM2TIEHPixAljjDGLFi0yM2bMMMYYU1xcbLp3726qq6vNypUrze23326Kiooa/O9x9913m+joaPPll1+a6upq88MPP5x1m8eNG2cef/xxc+LECbNnzx4TERFhPvzwwzNyyKVPexbSJPyyd7Flyxa6deuGw+E452UDAgI4duwYX3/9NcYYunXrRkhIiHf67bffTu/evQkICGDUqFHs3bu3zvKpqakEBwdz9dVXs3r1apKTk+nRowfNmzdn+vTp7Nixg5KSEj744AO6du1KYmIiAQEBxMfHExYWxl/+8hfvupKSkujSpQuBgYEMGzaMzp07M2TIEAICAhg+fDh79uxpcBsOHz7Mt99+i91up2fPnrRu3fqs31fbtm0JCAjg3nvvpaqqim+++abB+TMyMnj11Vd588036dq161m/z9GjRxMeHk5AQAAFBQUNbvOBAwf49NNPeeSRR2jRogU333wzY8aMITs7+6zrl0uTykKaBLfbzZo1a8jMzMTtdp/XsoMHD+auu+4iLS2NwYMH88c//pGjR496p3fo0MH7+uqrr+b48eN1lr/uuuu8r8vKyujUqZP3fatWrQgODsbj8VBWVkbHjh3rLNuxY8c6h6lO/6wWLVpYfvYv3G43UVFRTJ8+naioKP7t3/6N6urqBrf51VdfZcSIEfTv358BAwZw5MgRDh06dNb577rrLkJDQ71jc+fOpV+/fvTr148lS5Y0+H00tM1lZWUEBQXVKbVffx9y+VBZSJPQqVMnrr/+evLy8rznDk7XsmXLOie9f/zxxzrT77nnHlatWsV7771HUVERS5cuPefPttls3tchISHs37/f+/748eMcPnwYh8NBSEgI33//fZ1lDxw4cF57QQ256qqreOihh3jvvfd4++23+eCDD+qcxzndtm3bWLp0KQsXLuR///d/2bZtG4GBgZiz3ED6tdde409/+hPr16/3jqWlpbF9+3a2b9/O5MmTveO//j4a2uaQkBAqKirqFPPp38fp65FLn8pCmoynn36ajIwMrrnmmjOm3XzzzWzcuJGffvqJb7/9lpUrV3qnFRYWsnPnTqqrq2nZsiXNmzenWbML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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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55KdnU1+fj7bt2/n4MGDLFq0iPXr13Py5ElWrVrFW2+9hc1mIy4uDofDgY+Pzy+zBURExFK9YdG7d2969+7N2LFjad269QUv2M/PDz8/PwA6duxIcHAwTqeTzMxM/vrXvwIQGxvLpEmTmDt3LpmZmcTGxmKz2RgwYADl5eUUFxezZ88ehg8fjq+vLwDDhw9n165djB079mI+r4iIXATLx6rm5uayatUqvvnmG2pqajAMA5vNRmZmZoNXUlhYyJEjR7jhhhsoKSkxQ+TKK6+kpKQEAKfTib+/v9nH398fp9N5TrvdbsfpdDZ43SIi8vNZhsVjjz3G/Pnz6du3L61aXfiZthUVFcyaNYtHH32Ujh071plms9mw2WwXvEwREWlalt/+3t7ehIeH07VrVzp37mz+NER1dTWzZs0iOjqayMhIALp27UpxcTEAxcXFdOnSBfhhxFBUVGT2LSoqwm63n9PudDqx2+0N/4QiIvKzWYZFaGgoy5cvZ//+/Xz66afmjxXDMHjssccIDg5mypQpZrvD4SAtLQ2AtLQ0RowYUafdMAwOHDiAt7c3fn5+hIWFkZOTQ1lZGWVlZeTk5BAWFnaxn1dERC6C5W6ogwcPAvDJJ5+YbTabjddff/0n++3bt4/09HRCQkKIiYkBIDk5mWnTppGUlMSGDRvo1q0bK1asACA8PJysrCwiIiJo164dS5cuBcDX15f777+fhIQEAB544AHzYLeIiDQNy7D48cylCzVo0CA+++yz805bu3btOW02m42FCxeed/6EhAQzLEREpOlZhsWqVavO2z5jxoxfvBgREWmeLMOiffv25uvKykp27txJcHBwoxYlIiLNi2VY3H333XXeT506lalTpzZaQSIi0vxc8IUTZ8+erXMqq4iIXPosRxbR0dHm69raWkpLS3nggQcatSgREWleLMNi9erV///Mnp507doVT0/LbiIicgmx3A0VEBDAqVOneP/999mxYwd5eXlNUZeIiDQjlmGxdu1aHnroIUpKSigpKWHu3LkXfe2FiIi0TJb7kzZs2MC6devMU2h///vf85vf/IZJkyY1enEiItI8NOhsqH9/Ml5DnpInIiKXFsuRRVxcHImJiURERADw7rvvEh8f3+iFiYhI82EZFlOmTGHIkCHs27cPgGXLlnH99dc3emEiItJ8NOgc2O7du+Ph4YHL5cIwDD799NM6z+UWEZFLm2VYrFixgo0bN3L11VebbQ25RbmIiFw6LMNi69at7NixAy8vr6aoR0REmiHLs6FCQkI4depUU9QiIiLNlOXIYtq0acTGxhISEkLr1q3N9n+/DYiIiFzaLMNi3rx5/P73vyckJIRWrS74JrUiInIJsAyLtm3b8rvf/a4pahERkWbKMiwGDRrEn//8ZxwOR52D3Dp1VkTk8mEZFocPHwbgwIEDZptOnRURubxYhsX57jD73XffNUoxIiLSPDX4KUbl5eVs27aNzZs3869//YucnJzGrEtERJqRnwyL77//nszMTDZt2sSRI0eoqKjghRdeYPDgwZYLnj9/Pjt37qRr165s3rwZgJUrV7Ju3Tq6dOkCQHJyMuHh4QC8/PLLbNiwgVatWvH4449zyy23AJCdnc2TTz5JbW0tiYmJTJs27Wd9YBERuXD1hsWcOXPYu3cvw4cPZ9KkSdx8881EREQQGhraoAXHxcVx55138sgjj9Rpnzx5MlOnTq3TlpeXR0ZGBhkZGTidTqZMmcK2bdsAWLx4Ma+99hp2u52EhAQcDgfXXnvthX5OERH5GeoNi7y8PDp16kSPHj3o0aMHHh4e2Gy2Bi948ODBFBYWNmjezMxMoqKi8PLyIjAwkKCgIHJzcwEICgoiMDAQgKioKDIzMxUWIiJNrN6r7NLT01mxYgUVFRVMnjyZiRMnUlFR8bMPbr/xxhtER0czf/58ysrKAHA6nfj7+5vz2O12nE5nve0iItK0fvKS7B49ejBr1izeeecdHnvsMcaPH09CQgITJky4qJVNnDiRHTt2kJ6ejp+fH0899dRFLUdERJpWg8+G6tu3L3379uXhhx9m7969F7WyK664wnydmJjIfffdB/wwYigqKjKnOZ1O7HY7QL3tIiLSdC74Zk82m61BZ0OdT3Fxsfn63XffpWfPngA4HA4yMjKoqqqioKCA/Px8+vfvT79+/cjPz6egoICqqioyMjJwOBwXtW4REbl4DR5ZXKjk5GT27NnDiRMnuPXWW5k5cyZ79uzh6NGjAAQEBLB48WIAevbsyejRoxkzZgweHh4sWLAADw8PABYsWMA999yDy+UiPj7eDBgREWk6jRYWzz777DltiYmJ9c4/ffp0pk+ffk57eHi4eS2GiIi4h+VuqBdffNF8XVVV1ajFiIhI81RvWKSkpLB//37z4jiA3/zmN01SlIiINC/17oYKDg7mnXfeoaCggN/+9rcEBwdz8uRJvvzyS4KDg5uyRhERcbN6RxadOnUiOTmZoKAg/vrXv5oPQHrllVcu+joLERFpmeodWeTk5PDCCy/w9ddfs2zZMnr16kW7du1YtmxZU9YnIiLNQL0ji+TkZNauXUtAQAAxMTHU1tZSWlrKxIkTzYvpRETk8mB56mxYWBj9+vWjX79+vPnmm7z55puUlpY2RW0iItJMWJ46+/DDD5uvf7yX04/PoxARkcvDBd3uo3fv3o1Vh4iINGMXfG8oERG5/CgsRETEksJCREQsKSxERMSSwkJERCwpLERExJLCQkRELCksRETEksJCREQsKSxERMSSwkJERCwpLERExJLCQkRELCksRETEUqOFxfz58xk6dChjx441206ePMmUKVOIjIxkypQplJWVAWAYBk888QQRERFER0fz6aefmn02btxIZGQkkZGRbNy4sbHKFRGRn9BoYREXF8eaNWvqtKWkpDB06FC2b9/O0KFDSUlJASA7O5v8/Hy2b9/OkiVLWLRoEfBDuKxatYp169axfv16Vq1aZQaMiIg0nUYLi8GDB+Pj41OnLTMzk9jYWABiY2N5991367TbbDYGDBhAeXk5xcXF5OTkMHz4cHx9ffHx8WH48OHs2rWrsUoWEZF6NOkxi5KSEvz8/AC48sorKSkpAcDpdOLv72/O5+/vj9PpPKfdbrfjdDqbsmQREcGNB7htNhs2m81dqxcRkQvQpGHRtWtXiouLASguLqZLly7ADyOGoqIic76ioiLsdvs57U6nE7vd3pQli4gITRwWDoeDtLQ0ANLS0hgxYkSddsMwOHDgAN7e3vj5+REWFkZOTg5lZWWUlZWRk5NDWFhYU5YsIiKAZ2MtODk5mT179nDixAluvfVWZs6cybRp00hKSmLDhg1069aNFStWABAeHk5WVhYRERG0a9eOpUuXAuDr68v9999PQkICAA888AC+vr6NVbKIiNSj0cLi2WefPW/72rVrz2mz2WwsXLjwvPMnJCSYYSEiIu6hK7hFRMSSwkJERCwpLERExJLCQkRELCksRETEksJCREQsKSxERMSSwkJERCwpLERExJLCQkRELCksRETEksJCREQsKSxERMSSwkJERCwpLERExJLCQkRELCksRETEksJCREQsKSxERMSSwkJERCwpLERExJLCQkRELCksRETEkqc7VupwOOjQoQOtWrXCw8OD1NRUTp48yezZszl27BgBAQGsWLECHx8fDMPgySefJCsri7Zt2/LUU0/Rp08fd5QtInLZctvIYu3ataSnp5OamgpASkoKQ4cOZfv27QwdOpSUlBQAsrOzyc/PZ/v27SxZsoRFixa5q2QRkctWs9kNlZmZSWxsLACxsbG8++67ddptNhsDBgygvLyc4uJid5YqInLZcVtYTJ06lbi4OP7+978DUFJSgp+fHwBXXnklJSUlADidTvz9/c1+/v7+OJ3Opi9YROQy5pZjFm+++SZ2u52SkhKmTJlCcHBwnek2mw2bzeaO0kRE5DzcMrKw2+0AdO3alYiICHJzc+natau5e6m4uJguXbqY8xYVFZl9i4qKzP4iItI0mjwszpw5w+nTp83Xu3fvpmfPnjgcDtLS0gBIS0tjxIgRAGa7YRgcOHAAb29vc3eViIg0jSbfDVVSUsIDDzwAgMvlYuzYsdx6663069ePpKQkNmzYQLdu3VixYgUA4eHhZGVlERERQbt27Vi6dGlTlywictlr8rAIDAzk7bffPqe9c+fOrF279px2m83GwoULm6I0ERGpR7M5dVZERJovhYWIiFhSWIiIiCWFhYiIWFJYiIiIJYWFiIhYUliIiIglhYWIiFhSWIiIiCWFhYiIWFJYiIiIJYWFiIhYUliIiIglhYWIiFhSWIiIiCWFhYiIWFJYiIiIJYWFiIhYUliIiIglhYWIiFhSWIiIiCWFhYiIWFJYiIiIpRYTFtnZ2YwaNYqIiAhSUlLcXY6IyGWlRYSFy+Vi8eLFrFmzhoyMDDZv3kxeXp67yxIRuWy0iLDIzc0lKCiIwMBAvLy8iIqKIjMz091liYhcNjzdXUBDOJ1O/P39zfd2u53c3Nx65z927BhxcXGWyw36Rapr+eLi0n6hJfX8hZbTgjXg764h7Nh/keW0dHHv/zLbUxrm2LFj9U5rEWFxoT766CN3lyAicklpEbuh7HY7RUVF5nun04ndrv+8RESaSosIi379+pGfn09BQQFVVVVkZGTgcDjcXZaIyGWjReyG8vT0ZMGCBdxzzz24XC7i4+Pp2VP7x0VEmorNMAzD3UWIiEjz1iJ2Q4mIiHspLERExJLCQkTO8frrrzN69GjmzJnj7lKkmdAxCxE5x+23387//M//1LkYVi5vCgs3OHPmDElJSRQVFVFbW8v999/PmDFj3F1Wi/SXv/wFHx8fJk+eDMBzzz1Hly5duOuuu9xbWAu2YMECUlNTueaaa4iPjze3rVycF154gbfffpsuXbpw1VVX0adPH6ZOnerusi5Yizh19lKza9cu/Pz8zLvnnjp1ys0VtVzx8fHMnDmTyZMnU1tbS0ZGBuvXr3d3WS3a4sWLycnJYe3atXTp0sXd5bRoubm5bN++nbfffpvq6mri4uLo06ePu8u6KDpm4QYhISF88MEH/OlPf2Lv3r14e3u7u6QWq3v37izIGnwAAAbySURBVPj6+nL48GFycnK4/vrr6dy5s7vLEgHg448/ZsSIEbRp04aOHTty2223ubuki6awcINrrrmG1NRUQkJCWLFiBatWrXJ3SS1aYmIiqamppKamEh8f7+5yRC5JCgs3cDqdtGvXjpiYGKZOncrhw4fdXVKLNnLkSHbt2sWhQ4cICwtzdzkiphtvvJH333+fyspKKioq2Llzp7tLumg6ZuEGn3/+OU8//TStWrXC09OTRYsWubukFs3Ly4vQ0FA6deqEh4eHu8sRMfXv3x+Hw8G4cePo2rUrISEhLXa3s86GkhavtraW8ePH85e//IX/+q//cnc5InVUVFTQoUMHzp49yx133MGSJUta5EFujSykRcvLy+Pee+8lIiJCQSHN0oIFC8jLy6OyspLx48e3yKAAjSxERKQBdIBbREQsKSxERMSSwkJERCzpALe0KCdOnDDvVfTdd9/RqlUr85YU69evx8vLq9FrWL58OdnZ2dx666088sgjjb6+n6ul1SvNkw5wS4u1cuVK2rdv3+Q3ZbvpppvYs2dPs7+mo6amBk9PzxZTrzRvGllIi/b999/jcDjYtm0brVu35vTp04wbN45t27Zx991306tXL/75z3/icrlYunQp/fv358yZMyxZsoQvvviCmpoaZsyYwciRI+ss1zAMnn76aXbt2oXNZmP69OmMGTOG++67jzNnzhAXF8e9995b527BFRUVPPHEE3zyyScAzJgxg1GjRrFw4UIOHTpEZWUlo0aNYtasWQA4HA5uv/12du3aRZs2bfjzn/9MUFBQnTpcLhePPfYYn3zyCTabzbwL7KRJk3j44Yfp168fpaWlJCQk8N5775Gamsr27ds5c+YMtbW1dOzYsU69bdu25aWXXqK6uhpfX1+eeeYZrrjiinprz8nJYeXKlVRVVREYGMiyZcvo0KFDY/5KpZlSWEiL1rZtW0JDQ8nKymLkyJFkZGQQGRlJ69atgR/CJD09nX/+8588+uijbN68mdWrV3PzzTezbNkyysvLSUxMZNiwYbRv395c7vbt2zl69Cjp6emcOHGChIQEBg0axOrVqxk4cCDp6enn1PLiiy/SsWNHNm3aBEBZWRkAs2fPxtfXF5fLxeTJkzl69Ci9e/cGwNvbm02bNpGWlsbSpUt5+eWX6yzzyJEjOJ1ONm/eDEB5ebnlNjl8+DBvv/02vr6+AHXqLSsrY926ddhsNtavX8+aNWuYN2/eeWsvLS3lpZde4rXXXqN9+/akpKTw2muvMWPGjIb/guSSobCQFi8hIYE1a9YwcuRIUlNTWbJkiTktKioKgMGDB3P69GnKy8vJycnhvffe49VXXwWgsrKS48eP06NHD7Pfvn37iIqKwsPDgyuuuILBgwdz6NAhRowYUW8d//jHP3j22WfN9z4+PgBs3bqVdevWUVNTw7fffsu//vUvMyzGjh1r1rls2bJzlhkYGEhBQQFLliwhPDy8Qfe+Gj58uBkU/6moqIjZs2fz7bffUlVVRffu3eut/f333ycvL4+JEycCUF1dzYABAyzXL5cmhYW0eDfddBN//OMf+eijj3C5XISEhJjTbDZbnXl/fP/8888THBzc6LUVFBTw6quvsmHDBnx8fJg3bx6VlZX1zu9yuYiLiwN+2E314IMPkp6eTk5ODn/729/YunUry5Ytw8PDgx8PN1ZVVdVZRrt27epd/hNPPMHkyZMZMWIEH3300U/e8dgwDIYPH14nROTypVNn5ZIQGxvLnDlzzC/aH23ZsgXAfG6It7c3YWFh/O///q/5ZXu+u/4OGjSIrVu34nK5KC0tZe/evfTv3/8naxg2bBhvvPGG+b6srIyKigratWuHt7c33333HdnZ2XX6bN261axz4MCBeHh4kJ6eTnp6Og8++CClpaUYhsGoUaNISkoyaw0ICDCPL7zzzjsN3k6nTp3CbrcDkJaW9pO1DxgwgI8//pivvvoK+OEJj//3f//X4HXJpUUjC7kkREdHs2LFCnO3zo/atGlDbGwsNTU1LF26FID777+fpUuXMm7cOGpra+nevfs5xwoiIiLYv38/MTEx2Gw25s6dy5VXXvmTNUyfPp3FixczduxYWrVqxYwZM4iMjOT6669n9OjR+Pv7c+ONN9bpU1ZWRnR0NF5eXuf9D764uJj58+dTW1sLQHJyMgB33303SUlJrFu3jvDw8AZvpxkzZvDggw/i4+NDaGgohYWFP1n7smXLSE5ONkcvSUlJXHPNNQ1en1w6dOqsXBLeeecdMjMz+dOf/mS2/fsZQ82Rw+Fgw4YNenSptAgaWUiLt2TJErKzs81nmovIL08jCxERsaQD3CIiYklhISIilhQWIiJiSWEhIiKWFBYiImLp/wGytJTAE5PpmgAAAABJRU5ErkJggg==\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "def feature_class_plot(col: Optional[str] = None) -> Any:\n",
        "    \"\"\"\n",
        "    creating function to create countplots for features stacked with class\n",
        "    เปรียบเทียบของแต่ละคุณลักษณะ ว่ามีกลุ่มเป้าหมายมากน้อยแค่ไหน\n",
        "    \n",
        "    :param\n",
        "        - col\n",
        "    \"\"\"\n",
        "    \n",
        "    fig, ax = plt.subplots()\n",
        "    sns.set_style('darkgrid')\n",
        "    sns.countplot(col, hue=_class, palette=['black', 'lightgreen'], data=df)\n",
        "    plt.ylabel('# of comparison')\n",
        "    plt.xlabel('Type of {}'.format(col))\n",
        "    plt.title('Mushroom {}'.format(col))\n",
        "    plt.legend(title=None, labels=['Poisonous', 'Edible'])\n",
        "    plt.show()\n",
        "    \n",
        "feature_class_plot('cap-shape') # plot เปรียบเทียบ feature ระหว่าง poisonous, และ edible\n",
        "feature_class_plot('bruises') # plot เปรียบเทียบ feature ระหว่าง poisonous, และ edible\n",
        "feature_class_plot('odor') # plot เปรียบเทียบ feature ระหว่าง poisonous, และ edible\n",
        "feature_class_plot('gill-color') # plot เปรียบเทียบ feature ระหว่าง poisonous, และ edible\n",
        "feature_class_plot('spore-print-color') # plot เปรียบเทียบ feature ระหว่าง poisonous, และ edible\n",
        "feature_class_plot('population') # plot เปรียบเทียบ feature ระหว่าง poisonous, และ edible\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 1000
        },
        "id": "LBLCDYbMXnhE",
        "outputId": "62a7c8fe-8d60-4c95-dc46-824030b4e3d5"
      },
      "execution_count": 9,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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QCsiUj9VGBaJiYl8+eWXbNq0yTFNI9CKiNRPFV4NFRQUxMaNGzl69CijRo0iKCiI/Px8vv32W4KCgmqyRhERqWUVtiyaNGnClClTCAwMZPXq1YwZMwaApUuXMnLkyBorUEREal+FLYuMjAxef/11jhw5wvz582nbti2NGjXSDXkiIvVQhS2LKVOmsHLlSvz9/YmKiqKsrIxTp05x//33M378+JqsUUREapnhHdwhISF07NiRjh078t577/Hee+9pIEERkXrG8NLZadOmOX5fsGABcPHBSCIiUn9c10151/PEvBkzZhAcHMzQoUMd0wCzmeIAAAvSSURBVPLz84mLiyM8PJy4uDhOnz4NgN1u5/nnnycsLIzIyEgOHDjgWGfdunWEh4cTHh7OunXrrqdcERGpItV2B3dMTAzLli0rNy0xMZHg4GA2b95McHAwiYmJAGzfvp3MzEw2b97Mc889x5w5c4CL4fLaa6+xZs0aPvjgA1577TVHwIiISM2ptrDo2bMnTZs2LTctLS2N6OhoAKKjo9m6dWu56SaTiS5dulBQUEBOTg4ZGRn07dsXb29vmjZtSt++fdmxY0d1lSwiIhUw7OCuSrm5ufj6+gLg4+NDbm4uAFarFT8/P8dyfn5+WK3Wy6ZbLBasVqvhfsxmE97eHlVcvchFOrbEmVXX8VmjYfFzJpMJk8lULdu22ezk5xdVy7brIx8fr9ouwak4w7Gl70QqciPH59WOqxoddfaWW24hJycHgJycHMdVVRaLhezsbMdy2dnZWCyWy6ZbrVYsFktNliwiItRwWISGhpKcnAxAcnIyAwYMKDfdbrezb98+vLy88PX1JSQkhIyMDE6fPs3p06fJyMggJCSkJksWERGq8TTUlClT2L17N3l5efTr149JkyYxbtw4HnvsMdauXUvLli1ZvHgxAP379yc9PZ2wsDAaNWpEQkICAN7e3kyYMIHhw4cDMHHiRLy9vaurZBERqYDJbrfba7uIqlZSYnOK88p1hY+PF8uPLq/tMpxCfEA8J0+eqe0y8PHxqrY+v5uN3W7X8fmjGz0+nabPQkREbk4KCxERMaSwEBERQwoLERExpLAQERFDCgsRETGksBAREUMKCxERMaSwEBERQwoLERExpLAQERFDCgsRETGksBAREUMKCxERMaSwEBERQwoLERExpLAQERFDCgsRETFU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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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QkRETCksRETElMJCRERMKSxERMSUwkJEREwpLERExJTCQkRETCksRETElMJCRERMKSxERMSUwkJEREwpLERExJTCQkRETCksRETElMJCRERMKSxERMSUwkJEREwpLERExJTCQkRETCksRETElMJCRERMuSwsjh07xsiRIxk0aBDR0dGsWLECgFdffZU777yT2NhYYmNj2bp1q7PN66+/TlRUFP3792f79u3O5du2baN///5ERUWRkpLiqpJFRKQcnq7asNVqZerUqbRv357Tp0+TkJBA7969ARg9ejSJiYll3v/dd9+xbt061q1bh91uZ8yYMWzcuBGA2bNns3z5cmw2G8OGDSMyMpJWrVq5qnQREfkPLguLwMBAAgMDAWjUqBFhYWHY7fZy35+enk50dDReXl6EhIQQGhrKvn37AAgNDSUkJASA6Oho0tPTFRYiItXIZWFxsSNHjvD111/TuXNnvvzyS1auXElaWhodOnRg6tSp+Pn5Ybfb6dy5s7ONzWZzhktQUFCZ5RdCpDxWqwV//4au+TBSp+g4Einl8rAoKChg0qRJTJ8+nUaNGvHggw8yfvx4LBYLixcvZv78+cybN69K9+lwGOTlFVbpNqV2Cgjwvab2Oo6kLqno34tLr4YqLi5m0qRJDB48mH79+gHQrFkzrFYrHh4eDB8+nP379wOlPYbs7GxnW7vdjs1mK3e5iIhUH5eFhWEYzJgxg7CwMMaMGeNcnpOT4/x5y5YttG7dGoDIyEjWrVtHUVERWVlZZGZm0qlTJzp27EhmZiZZWVkUFRWxbt06IiMjXVW2iIhchulpqKKiIjZu3MjRo0cpKSlxLn/88ccrbLd7927Wrl1LmzZtiI2NBSApKYmPP/6YQ4cOARAcHMzs2bMBaN26NQMHDmTQoEFYrVZmzpyJ1WoFYObMmTz66KM4HA4SEhKcASMiItXDYhiGUdEbEhMT8fX1pX379s5f3gCPPPKIy4urrOJih841C1B6DnZZ1rJKtU0MSeTnn09VcUUiNVdFYxamPQu73c6yZZX7xyYiItcH0zGLrl278s0331RHLSIiUkOZ9ix2797NmjVrCA4OxsvLy7n8o48+cmlhIiJSc5iGxRtvvFEddYiISA1mehoqODiYU6dO8emnn/Lpp59y6tQpgoODq6M2ERGpIUzDYsWKFfzud78jNzeX3Nxcnn76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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "iVBORw0KGgoAAAANSUhEUgAAAYsAAAEWCAYAAACXGLsWAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADh0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uMy4yLjIsIGh0dHA6Ly9tYXRwbG90bGliLm9yZy+WH4yJAAAgAElEQVR4nO3de1yUZf7/8RcMIMfAE5iImqZt4TExFw+YKLoKCIn+0jbbzDIPaYYdRF2PeapvqZmbx8o2t11rCVQ0TVLRzTQ1Uyt31xIFk8FUUkBFhvn94df5SoLDaYbT+/l48HjM3HPd9/W5Z4DPXIf7uh3MZrMZERGRO3Cs7ABERKTqU7IQERGrlCxERMQqJQsREbFKyUJERKxSshAREauULKTWmDx5MosWLarsMMpt+fLlTJ06FYD09HTuu+8+8vPzARg+fDgff/xxhda3b98+QkJCKvSYUv04VXYAIjeFhoaSmZlJSkoK9erVs2yPjo7mhx9+IDk5mSZNmlRihFXD6NGjKzsEqYXUspAqxd/fn6SkJMvzf//731y5csXucdz8pi7lYzKZKjsEqSBKFlKlREVFkZCQYHmekJBAdHR0oTK/7WqJj49n2LBhAJjNZubNm0dwcDAPPvggkZGR/Oc//7GUvXTpEqNGjaJjx44MGTKE06dPW1677777WLduHX379qVv374ArF+/nrCwMB566CFGjx6N0Wi0lD906BAxMTF06tSJmJgYDh06VCjGRYsWMXToUDp27Mjo0aO5ePEikyZN4sEHHyQmJob09PRi34eEhAR69epFly5dWLZsGaGhoXz55ZcALF26lBdffLFU7+tN//3vfxkxYgQPPfQQXbt2Zfny5QDk5eUxd+5cunfvTvfu3Zk7dy55eXlFHuPHH39k+PDhBAUFER4eTnJysuW1yZMnM2PGDJ555hk6dOjAvn37yhSnVD1KFlKldOjQgezsbH788UdMJhNJSUkMHDiwxPvv2bOHAwcOsHXrVg4ePMjixYvx8fGxvL5582aee+45vv76a5o2bXrbGMb27dtZv349mzdvZu/evbzxxhssXryYPXv24O/vT2xsLABZWVk8++yzDB8+nH379jFixAieffZZLl68WKiu1157jZSUFE6fPs3QoUOJiYlh//79tGzZkmXLlhV5DidOnGDWrFm8/vrr7N69m+zs7EJJqqyys7MZMWIEPXr0YPfu3Wzbto3g4GAA3nnnHb799lsSExPZsGEDR48e5S9/+cttx7h+/TqjR4+mW7dufPnll0ybNo0XX3yRn376yVJm06ZNjB49mkOHDtGpU6dyxy1Vg5KFVDk3Wxf/+te/aNmyJX5+fiXe18nJiZycHH766SfMZjMtW7bE19fX8nqfPn1o164dTk5ODBw4kB9++KHQ/qNGjcLHxwdXV1c2btxITEwMgYGBuLi4EBsby+HDh0lPT2fnzp00a9aM6OhonJyciIiIoEWLFuzYscNyrEGDBtG0aVO8vLwICQkhICCArl274uTkxB/+8Ae+//77Is/hs88+o1evXgQFBeHi4sKECRNwcHAo5bt4u507d9KgQQOeeuop6tSpg6enJ+3btwdg48aNjBs3jvr161OvXj3GjRvHhg0bbjvGt99+S25uLqNGjcLFxYXg4GB69epVqOuwd+/edOrUCUdHR+rUqVPuuKVq0AC3VDlRUVE8/vjjpKenExUVVap9g4OD+eMf/8js2bM5c+YMffv25ZVXXsHT0xOABg0aWMq6urqSm5tbaP+7777b8jgzM5PAwEDLcw8PD3x8fDAajWRmZtK4ceNC+zZu3LhQC+DWuurUqWO17lvrbdSokeW5m5tbodZRSYWHh/Pzzz8DsGrVKs6ePUvTpk2LrfPW82ncuDGZmZnFxubo6Fio7K3nfet7KDWHWhZS5fj7+9OkSRN27dplGTu4lZubW6FB719++aXQ60888QTx8fFs3ryZ1NRUVq9eXeK6b/0G7+vry5kzZyzPc3NzycrKws/PD19fX8s/4pvOnj1bqlZQcXx9fQv987169SpZWVmlPk5SUhLffPMN33zzDUFBQdx9992kpaUVW+et53P27NlCLbJby2VkZFBQUFCobEWct1RtShZSJc2dO5e1a9fi7u5+22v3338/n3/+OVeuXOHUqVN88sknlteOHDnCt99+y/Xr13Fzc8PFxaXQt+DSiIiIID4+nh9++IG8vDzefPNN2rVrR5MmTejZsyepqals3LiR/Px8Nm/ezIkTJ3j44YfLesoW/fr144svvuDQoUPk5eWxdOlSKuJOAg8//DDnzp3j/fffJy8vj+zsbL799lvgRivknXfe4cKFC1y4cIFly5YRGRl52zHatWuHq6srq1ev5vr16+zb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7Joshg0bxtixY3FwcGDJkiUsWLCA+fPn26Quk8l822woAG9vX5vUV1tlZ18HrE8ZltrpTrNrilLU36zYT5WZDdWgQQMMBgOOjo4MGTKEo0ePAjdaDBkZGZZyRqMRPz+/YreLiIh92TVZZGZmWh5v376dVq1aARAaGkpSUhJ5eXmkpaWRmppKu3btaNu2LampqaSlpZGXl0dSUhKhoaH2DFlERLBhN1RsbCz79+/n4sWLhISEMH78ePbv38/x4zeWn/D392f27NkAtGrViv79+zNgwAAMBgPTp0/HYDAAMH36dJ5++mlMJhMxMTGWBCMiIvZTq67gFhH70hXc1UuVGbMQEZHqSclCRESsUrIQERGrlCxERMQqJQsREbFKyUJERKxSshAREauULERExColCxERsUrJQkRErFKyEBERq5QsRETEKiULERGxSslCRESsUrIQERGrlCxERMQqJQsREbFKyUJERKxSshAREauULERExColCxERscrJVgeOi4tj586d1K9fn02bNgGwcOFCduzYgbOzM02bNmX+/PncddddpKenM2DAAO655x4A2rdvz+zZswE4duwYcXFxXL16lZ49ezJ16lQcHBxsFbaIiBTBZi2LQYMGsXr16kLbunXrxqZNm9i4cSPNmzdnxYoVlteaNm1KYmIiiYmJlkQBMHPmTObMmcO2bdtITU0lJSXFViGLiEgxbJYsOnfujLe3d6Ft3bt3x8npRmOmQ4cOZGRk3PEYmZmZZGdn06FDBxwcHIiOjiY5OdlWIYuISDFs1g1lzT//+U/69+9veZ6enk50dDSenp5MnDiRoKAgjEYjjRo1spRp1KgRRqOxRMc3GBzw8XGv8LhFxHb0N1t1VUqyeOeddzAYDAwcOBAAX19fduzYQd26dTl27Bjjxo0jKSmpXHWYTGaysnIrIlwRKaOGDb1KVV5/s5XrTp+X1WSRl5fH1q1bOXPmDPn5+Zbtzz33XJmCiY+PZ+fOnbz//vuWgWoXFxdcXFwAaNOmDU2bNuXkyZP4+fkV6qrKyMjAz8+vTPWKiEjZWR2zGDNmDMnJyRgMBtzd3S0/ZZGSksLq1at55513cHNzs2y/cOECJpMJgLS0NFJTUwkICMDX1xdPT08OHz6M2WwmISGB3r17l6luEREpO6stC6PRyJo1a0p94NjYWPbv38/FixcJCQlh/PjxrFy5kry8PEaMGAH83xTZr7/+mrfeegsnJyccHR2ZNWsWPj4+AMyYMcMydTYkJISQkJBSxyIiIuXjYDabzXcq8Oc//5nHH3+c++67z14xVYjr103q/xSpZA0bepX4uiiz2cy5c5dtHJHcSbnGLA4ePMinn36Kv7+/ZVwBYOPGjRUTnYiIVHlWk8WqVavsEYeIiFRhVge4/f39uXz5Mjt27GDHjh1cvnwZf39/e8QmIiJVhNVksXbtWl588UXOnz/P+fPneemll/jrX/9qj9hERKSKsDrAHRkZyT/+8Q/LdNnc3FweffTRKj9moQFukcqnAe7q5U4D3CVaG8pgMBT5WEREagerA9yDBg1iyJAhhIWFYTabSU5OJiYmxh6xiYhIFWG1Gwrgu+++4+DBgwAEBQXxwAMP2Dyw8lI3lEjlUzdU9VKu6yxOnz5Nq1atCAwM5KuvvuLAgQM0adKEu+66q0KDFBGRqsvqmMX48eNxdHTk1KlTzJgxg7NnzzJp0iR7xCYiIlWE1WTh6OiIk5MT27Zt4/HHH+eVV17h3Llz9ohNRESqCKvJwsnJiU2bNpGYmMjDDz8MUGipchERqfmsJov58+dz+PBhRo8eTUBAAGlpaZabFomISO1QotlQ1ZFmQ4lUPs2Gql7KNBvq+eefZ8mSJURGRhb5elW/gltERCpOsS2LzMxMfH19OXPmTJE7VvXFBNWyEKl8allUL2VqWfj6+mIymZg8ebIWDhQRqeXuOMBtMBhwdHTk8mVlexGR2szqFdzu7u5ERkbStWtXy8qzANOmTbNpYCIiUnVYTRZ9+/alb9++9ohFRESqKE2dFRGb0QB39VKuhQRTU1N58803OXHiBNeuXbNsT05OtlpxXFwcO3fupH79+mzatAmArKwsXnjhBc6cOYO/vz+LFy/G29sbs9nM3Llz2bVrF66urixYsIDAwEAAPv30U9555x0AxowZwyOPPGK1bhERqThWr+COi4tj2LBhGAwGPvjgA6Kjo0t8BfegQYNYvXp1oW0rV64kODiYbdu2ERwczMqVKwFISUkhNTWVbdu2MWfOHGbOnAncSC5vv/0269ev5+OPP+btt9/m119/LeVpiohIeVhNFteuXSM4OBi4cW3F+PHj2bVrV4kO3rlzZ7y9vQttS05OJjo6GoDo6Gi2b99eaLuDgwMdOnTg0qVLZGZmsmfPHrp164aPjw/e3t5069aN3bt3l+okRUSkfKx2Q7m4uFBQUECzZs348MMP8fPzIycnp8wVnj9/Hl9fXwAaNmzI+fPnATAajTRq1MhSrlGjRhiNxtu2+/n5YTQardZjMDjg4+NutZyIVB36m626rCaLKVOmcOXKFaZNm8aSJUv46quvWLhwYYVU7uDgUOLBr9Iymcwa4BapZHcaMC2K/mYrV7kGuNu1awfcmKkwdepUPD09yxVM/fr1LUuJZGZmUq9ePeBGiyEjI8NSLiMjAz8/P/z8/Ni/f79lu9Fo5KGHHipXDCIiUjpWxyyOHj1KZGQkAwcOtPwcO3aszBWGhoaSkJAAQEJCAr179y603Ww2c/jwYby8vPD19aV79+7s2bOHX3/9lV9//ZU9e/bQvXv3MtcvIiKlV6JuqBkzZhAUFATAgQMHiIuLK9Gqs7Gxsezfv5+LFy8SEhLC+PHjGTVqFBMnTuSTTz6hcePGLF68GICePXuya9cuwsLCcHNzY968eQD4+PgwduxYBg8eDMC4cePw8fEp8wmLiEjpWb0oLzo62tISuOmRRx7h008/tWlg5aWL8mof73quuBicS1Q2z3SdXy9ctXFEoovyqpdyjVl07tyZ6dOnEx4ejoODA5s3b+ahhx7iu+++A7BcOCdS2VwMzqxJW1OisiMDRgJKFiIlZTVZHD9+HIC333670Pbvv/8eBwcHPvjgA9tEJiIiVYbVZKF7WdhOabpNQF0nIlJ5rCaLS5cukZCQwJkzZzCZTJbtWqK8/ErTbQLqOhGRymM1WYwaNYr27dvTunVrHB2tzrQVEZEayGqyuHbtGnFxcfaIRUREqiirTYWoqCjWr19PZmYmWVlZlh8REak9rLYsnJ2dee2111i+fLllm4ODQ4nuZyEiIjWD1WTx7rvvsm3bNssaTiIiUvtY7YZq1qwZbm5u9ohFRESqKKstCzc3N6Kjo+nSpQsuLi6W7Zo6KyJSe1hNFn369KFPnz72iEVERKooq8nikUceIS8vj9TUVADuuecenJ1LftWx1F5a2E+k5rCaLPbt28fkyZPx9/fHbDZz9uxZFi5cSOfOne0Rn1RjWthPpOawmiwWLlzImjVraNGiBQAnT55k0qRJxMfH2zw4ERGpGqzOhrp+/bolUcCNbqjr16/bNCgREalarLYs2rRpw9SpUxk4cCAAGzdupE2bNjYPTEREqg6ryWLWrFmsW7fOslR5UFAQjz32mM0DExGRqsNqssjPz+eJJ55gxIgRAJhMJvLy8mwemIiIVB1WxyyefPJJrl79v1kqV69etSQOERGpHawmi2vXruHh4WF57uHhwZUrV2walIiIVC0lWu7ju+++IzAwEIBjx47h6upa5gp/+uknXnjhBcvztLQ0JkyYwOXLl1m/fr1lwcLY2Fh69uwJwIoVK/jkk09wdHRk2rRp9OjRo8z1i4hI6VlNFlOmTOH555/H19cXs9nML7/8wqJFi8pcYYsWLUhMTARujH+EhIQQFhZGfHw8Tz75JCNHjixU/sSJEyQlJZGUlITRaGTEiBFs3boVg8FQ5hhERKR0rCaLdu3asWXLFk6ePAlU7HIfe/fuJSAgAH9//2LLJCcnEx4ejouLCwEBATRr1owjR47QsWPHColBRESss5os4MYNkFq3bl3hlSclJREREWF5vm7dOhISEmjTpg2TJ0/G29sbo9FI+/btLWX8/PwwGo1Wj20wOODj417hMVe2mnhON9n73Grye1ld6TOpukqULGwhLy+PL774gkmTJgEwbNgwxo4di4ODA0uWLGHBggXMnz+/zMc3mcxkZeVWVLg20bChV6n3qerndKvSnl95z83e9Yl1+kyqlzt9XsXOhjp48CCAza6pSElJITAwkAYNGgDQoEEDDAYDjo6ODBkyhKNHjwI3WhIZGRmW/YxGI35+fjaJSUREilZsspg7dy4Ajz76qE0qTkpKIjw83PI8MzPT8nj79u20atUKgNDQUJKSksjLyyMtLY3U1FTatWtnk5hERKRoxXZDOTk58ec//xmj0cirr7562+vluVNebm4uX375JbNnz7Zse/311zl+/DgA/v7+ltdatWpF//79GTBgAAaDgenTp2smlIiInRWbLJYvX87evXvZs2eP5RqLiuLu7s6+ffsKbXv99deLLT9mzBjGjBlToTGIiEjJFZss6tWrR3h4OC1btuR3v/udPWMSEZEqxupyHz4+PowbN47g4GCCg4MZP358oQFnERGp+awmi7i4OEJDQ9m9eze7d++mV69exMXF2SM2ERGpIqwmi/PnzxMTE4OTkxNOTk4MGjSICxcu2CM2ERGpIqwmi7p165KYmIjJZMJkMpGYmIiPj489YhMRkSrCarKYN28eW7ZsoVu3bnTv3p2tW7eW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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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MAB/f388PDzIyMi44rrVcXzEfeg0lLhMq1atypz38MMPc+jQIfr06UP79u2Jjo52dP4WFhYyf/58MjMz8fLyIiwsjLFjx15TDX369GHKlClMmjSJgoICunXr5ugQb9y4McuXL2f+/PnMmTOH4OBgli9fjr+//zXt60p69epFREQEdrudRx55hLCwMAAmTJhAYWGho+N/4MCBTJgwwbFe06ZNadSoEXfccQc+Pj7MmTOHNm3aAOUfu9975plnWLRoEXPnzqVXr17cc889FBQUABdbKOPHj2fUqFFcuHDhstZbdRwfcR8mPfxIpPplZmYyYMAADh8+jKdnxf5m2717N08++SQ7d+6soupELqfTUCIiYkhhISIihnQaSkREDKllISIihmrl1VAlJSXYbGowiYhURJ065jLn1cqwsNns5OfrTlERkYoICGhY5jydhhIREUMKCxERMaSwEBERQ7Wyz0JEaheb7QJ5eb9w4UKRq0upFTw9vWjcOACz+eojQGEhIm4vL+8XvL3rUb9+M8PRcKV8drudwsIC8vJ+oWnTG4xX+F86DSUibu/ChSLq12+koHACk8lE/fqNKtxKU1iISI2goHCeazmWCgsRETGkPgsRqXHq1zdf8aFZ1+rs2bMUFtrKnN+vXy9CQm7CZrtAcHBrnn76b3h7e19x2dTUHfz000+MHj3GafW5A4WFiBtw9i+/3zL6RVgT1atXz6mnpS52+p4uc37dunX5r//6bwD+9renSUxcx8iRD15x2bCw/oSF9Xdabe5CYSHiBpz9y++3jH4RSsV06dKVI0eOUFBwigUL5vLzz8epW9ebadNmcdNNbdmyZSPffPMV8fFP8c9/JvPWWyvw8DDToEEDXnllJefPn+eFFxbyzTdfYTabmTQpnttu68GWLRtJTd3Jr7/+ys8/Z9Kv351MmDAFgE8+2cqaNW9ht9sJDQ1jwoTJAERE3MEnn+wC4F//Suazz1KZNWvOFfdbWQoLEZGrdOHCBb744jN69+7DqlWv07ZtexYseIF9+/7Dc88942h9XPJf/7WSF1/8BwEBgZw+fTGwExI+BODttz/g6NF0/vKXibz3XgIA33//HW+99S516tTh/vtjiY0dgdls5rXXlrFq1Ts0bNiQ+PjH2bnzU/r1u7PMOq+038pSB7eIiIHz588zZsz9PProQ1gszRg8eChpaQe4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6+uLj48PP/zwAwcOHHB1SU41aNAgtmzZwrZt2xg4cKCry3G6u+66i127dnHo0CHCwsJcXY5T3X777WzdupW8vDwA8vPzXVyRWhaV8t1337F48WI8PDzw9PRkzpw5ri5JKqBfv368//773HPPPbRu3ZquXbu6uiSnatu2LYWFhQQGBhIYGOjqcpzOy8uL3r1706hRI8xms6vLcaq2bdsyfvx4Ro8ejYeHB7feeisLFy50aU0a7kNEaqSSkhLuvfdeli5dyo033ujqcmo9nYYSkRrnyJEjREREEBoaqqCoJmpZiIiIIbUsRETEkMJCREQMKSxERMSQLp2VGisvL88xNtCJEyfw8PBw3PH64YcflrqhsKosWrSInTt30q9fP5566qkq399vXc14T8nJydx4443cdNNNwMUxlXr27EmfPn2qq0ypJRQWUmM1btyYpKQkwHUD5a1du5Y9e/a47XX+ycnJ3HnnnY6wmDJliosrkppKp6Gk1vj1118JDw+nuLgYgDNnzjjejx49mueee46hQ4cyePBg0tLSgIsjB8+YMYP77ruPmJgYkpOTL9uu3W5n0aJFDB48mOjoaLZs2QLA+PHjOXv2LMOGDXNMu2TZsmU8+eSTjBgxgsjISNauXVvutnbv3s0DDzzAuHHjuPvuu5k9ezYlJSUAdOvWzbHdrVu3Mn369MtqXLt2LbGxsQwZMoRJkyZx7tw59u/fzz//+U8WL17M0KFDOXbsGNOnT2fr1q0AfP7558TExBAdHc2MGTMoKioCIDw8nJdffpl7772X6Ohofvjhh2v/T5FaQ2EhtYa3tze9e/dmx44dAGzevJnIyEjq1KkDXAyTpKQknnnmGWbOnAnA8uXLuf3221m3bh1vv/02zz//PGfPni213e3bt/PNN9+QlJTEW2+9xeLFi8nJyWH58uV4e3uTlJR0xbGXvv32W1avXs3777/PK6+8gtVqLXNbAGlpafz1r39ly5YtZGRksH379qv+7BEREXz00Uds2LCBkJAQ1q1bx2233UZ4eDjTpk0jKSmJVq1aOZY/f/4806dP56WXXmLjxo3YbDb++7//2zG/cePGrF+/npEjR/Lmm29edR1SeykspFa57777+OijjwBISEhg2LBhjnlRUVEA9OzZkzNnzlBQUEBqaiorV65k6NChjoEEs7KySm1z3759REVFYTabadq0KT179uTQoUOGtQwYMABvb2/8/f3p3bs3hw4dKndbnTt3JigoCLPZTFRUFPv27bvqz/39999z//33Ex0dzcaNG/n+++/LXf6nn36iZcuWtG7dGoB7772XvXv3OuZHRkYC0LFjx1o30rBcG/VZSK3SvXt3/va3v7F7925sNhvt2rVzzDOZTKWWvfT+5ZdfJiQkxOm1/H5/FV3+SuuXNSru9OnTefXVV7n55ptJSEhgz549Fdr3711qjXl4eGCz2Sq1Lakd1LKQWicmJoapU6eWalUAjv6BvXv30rBhQxo2bEhYWBjvvPMOlwYyuNLIwT169ODjjz/GZrNx8uRJ9u7dS+fOnQ3rSElJ4fz58+Tl5bFnzx46depU7rbS0tLIyMigpKSEjz/+mO7duwPQtGlTfvjhB0pKSq7YpwJQWFhIQEAAxcXFbNy40TG9fv36FBYWXrZ869atOX78OEePHgUgKSmJnj17Gn4muX6pZSG1TnR0NEuWLGHw4MGlptetW5eYmBguXLjA/PnzgYuPVZ0/fz5DhgyhpKSEli1b8vrrr5daLyIigi+//JKhQ4diMpl48sknCQgIMKyjffv2PPTQQ+Tl5TFhwgQsFkuZ2/rxxx/p1KkTzz77LEePHqV3795EREQAMHXqVB577DH8/f3p2LHjZX0qcPEqp+HDh+Pv70+XLl0cATFo0CD++te/smbNGl5++eVSx2LBggVMmTIFm81Gx44dGTVqVMUOtFxXNDaU1Dpbt24lJSWl1DOLR48ezbRp0+jUqVO11FDRS3l3797Nm2++eVlQibgLtSykVnn22WfZuXMnK1ascHUpIrWKWhYiImJIHdwiImJIYSEiIoYUFiIiYkhhISIihhQWIiJi6P8DkWC5pWA1/8IAAAAASUVORK5CYII=\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {}
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "def compare_feature_class(col: str) -> Any:\n",
        "    compare = sns.catplot(col, col='class', data=dft, kind='count', col_wrap=4)\n",
        "    compare.set_axis_labels(\"odor type\", \"# of compare\")\n",
        "    compare.set_titles(\"{col_name} {col_var}\")\n",
        "    compare.despine(left=True) \n",
        "    plt.show()\n",
        "    \n",
        "compare_feature_class('cap-shape') # plot เปรียบเทียบอีกรูปแบบหนึ่งซึ่งจะแบ่งกราฟออกเป็นสองส่วนอย่างชัดเจน\n",
        "compare_feature_class('bruises')\n",
        "compare_feature_class('odor')\n",
        "compare_feature_class('gill-color')\n",
        "compare_feature_class('spore-print-color')\n",
        "compare_feature_class('population')\n",
        "\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 1000
        },
        "id": "Q0LD5xOZXomO",
        "outputId": "3f263803-60e0-4c72-9795-6321b00eece3"
      },
      "execution_count": 10,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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9UxlAAAAgJ9wG8p3797tjToAAAAAv+U2lJ87d04FBQU6deqUmpubXe0PP/ywRwsDAAAA/IXbUD579mwNHjxY/fv3V0CA28+FAgAAAGglt6G8oaFBWVlZ3qgFAAAA8Etul77T0tL00ksvqbq6WjU1Na7/AQAAAGgbblfKg4KC9Oijj2r79u2uNovFotLSUo8WBgAAAPgLt6H83//933XgwAF169bNG/UAAAAAfsft9pXevXsrJCTEG7UAAAAAfsntSnlISIjS09N15513Kjg42NXOLREBAACAtuE2lI8ZM0ZjxozxRi0AAACAX3IbyidNmqTGxkaVl5dLkvr06aOgoKDr6jwrK0tvvfWWunfvrqKiIklSTU2NFi9erFOnTqlnz57avHmzwsPD5XQ6tXbtWh08eFCdO3fWhg0bFBcXJ0l67bXXtG3bNknS3LlzNWnSpBu5VgAAAKBdcrun/L333tO4ceOUk5Oj1atXa9y4cXr//fevq/PJkyfrqaeeatGWl5en+Ph4HThwQPHx8crLy5MkHTp0SOXl5Tpw4IDWrFmjVatWSfo2xOfm5uqll17Syy+/rNzcXJ09e7aVlwkAAAC0X25D+caNG/X000/rueee0549e/T0009r/fr119X58OHDFR4e3qKttLRU6enpkqT09HSVlJS0aLdYLBoyZIjOnTun6upqHTlyRCNGjFBERITCw8M1YsQIHT58uLXXCQAAALRbbrevNDU1KTY21vW4T58+ampquuETnj59WlFRUZKkyMhInT59WpLkcDgUHR3tOi46OloOh+OydrvdLofD4fY8NlsnWa2BN1xnexIREWq6BOCKGJv+y5fmWImxjPaLsek/3IbyAQMGaPny5Zo4caIkae/evRowYECbnNxischisbRJX3+trq7hsrbIyDCPnMvTamrOmy4BXtARxydj07e0ZgxeaY5tbR/tCWPZ9zE20R5caxy63b6yevVq9e3bV7t379bu3bvVt29frV69+oaL6d69u6qrqyVJ1dXVri8lstvtqqqqch1XVVUlu91+WbvD4ZDdbr/h8wMAAADtjdtQfvHiRd1///3Kzc1Vbm6uZsyYoebm5hs+YWJiogoKCiRJBQUFGj16dIt2p9Opjz76SGFhYYqKitLIkSN15MgRnT17VmfPntWRI0c0cuTIGz4/AAAA0N64DeUPPPCA6uvr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\n",
            "text/plain": [
              "<Figure size 1440x360 with 2 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 1440x360 with 2 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 1440x360 with 2 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 1440x360 with 2 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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u1asWOGPbAAAAEBQaPPTV76VkJCg5557zh9ZAAAAgKDkdaccAAAAgG9RygEAAADDKOUAAACAYV7PKf/P//xPTZ8+XZLU2Ngou91+RRN+/vnnmj17tudyZWWlHnroIZ05c0avvfaaunTpIkmaM2eO0tLSJEkbNmzQ66+/rpCQEC1cuFC33377FWUAAAAAAkmbO+UFBQX66KOPtH37ds/Y3XfffcUTJiUlyel0yul0qrCwUOHh4crIyJAk3X///Z7rvi3khw8fVklJiUpKSvT888/rscceU3Nz8xXnAAAAAAJFmzvlSUlJeuutt1RZWal77rlHSUlJOnnypD7//HMlJSW1y+QffPCBEhMTlZCQ0OYxZWVlyszMlN1uV2Jionr06KEDBw7olltuaZcMAAAAgGltlvJOnTppzpw52rt3rzZv3qw///nPeu+99/SrX/1KR44c0auvvnrFk5eUlCgrK8tzecuWLSoqKlLfvn01b948RUdHy+VyacCAAZ5jHA6HXC6X1/uOjOwgm816xRkR3GJiIkxHOE8gZkLwCaQ1NhDfE4GYCUBga7OU7969W//xH/+hL7/8UitWrNBPfvIThYeHt9sXBzU2NuoPf/iD5s6dK0maOHGipk+fLovFojVr1mjlypVXNFdtbUO75IR5sbFRxuY+efLsD44HYibgSl3K6/r7a2wgvicCMROA4HahdanNc8rnzJmjTZs2KSEhQdnZ2WppadHx48c1ceJEPfjgg1ccaufOnUpOTla3bt0kSd26dZPValVISIjy8vJ08OBBSd/sjFdXV3tu53K55HA4rnh+AAAAIFB4/UjEoUOHql+/frr77rsVHx+vV155RcuXL7/iiUtKSpSZmem5XFNT4/m5tLRUvXv3liSlp6erpKREjY2NqqysVEVFhfr373/F8wMAAACBwutHIj788MOen1euXClJno8tvFxnz57V+++/r6VLl3rGnnrqKX322WeSpISEBM91vXv31qhRozR69GhZrVYtWrRIVmtgnMcIAAAAtAevpfy7brzxxnaZNCIiQnv27Gk19tRTT7V5/LRp0zRt2rR2mRsAAAAINHyjJwAAAGAYpRwAAAAwjFIOAAAAGEYpBwAAAAyjlAMAAACGUcoBAAAAwyjlAAAAgGGUcgAAAMAwSjkAAABgGKUcAAAAMIxSDgAAABhGKQcAAAAMo5QD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7bXX5Ha7ddddd2nQoEFqaWnRm2++qaKiIjU3N2vcuHFKTk6WJD388MN69NFHNWjQIK1Zs0br1q3TggULJElNTU0qLCxsNcf111+vn/3sZ4qIiNCUKVMkSbfddpt27NihESNGqKSkRHfeeadCQ0MlSfX19XI6nfrwww81f/58FRcX67nnntPf//3fa8WKFTp9+rTy8vL005/+VBEREX58tgDg0rHOAudjpxz4nv3792vEiBGKiIhQx44dlZGRoX379mnfvn0aMWKEwsPDFRkZqfT0dEnSmTNndObMGQ0aNEiSNG7cOO3bt89zf6NHj76oeSdMmKA33nhDklRYWKjc3FzPdZmZmZKk1NRU1dbW6vTp09q9e7d+9atfKTs7W5MmTVJDQ4O++uqrdnkOAMCXWGeB87FTDvhYeHj4RR1366236rHHHtOePXvU3NysPn36eK6zWCytjv328rPPPqukpKT2CwsAVyHWWVwL2CkHviclJUWlpaWqq6vT2bNnVVpaqpSUFKWmpqq0tFT19fWqra3VO++8I0mKiopSp06dPLs2TqdTqampXufp2LGjvv7661ZjOTk5mjt3bqvdG0l68803JUn79u1TVFSUoqKiNHToUL388sv69kt5Dx06dMWPHQD8gXUWOB875cD3JCcnKzc3V3l5eZK++efOm2++WdI3/0SanZ2tLl26qF+/fp7brFq1SosXL1ZdXZ0SExO1YsUKr/Pccccdeuihh1RWVqZHH31UKSkpGjNmjFavXq2srKxWx3bo0EE5OTk6d+6cli9fLkmaPn26li9frrFjx6qlpUXXX3+9NmzY0F5PAwD4DOsscD6L+9u//gEw7q233lJZWZmeeuopz9ikSZP08MMPt/rDCQBweVhnEajYKQcCxOOPP66dO3eqoKDAdBQAuCaxziKQsVMOAAAAGMZ/9AQAAAAMo5QDAAAAhlHKAQAAAMMo5QAAAIBhlHIAAADAsP8Hkt8udobndqAAAAAASUVORK5CYII=\n",
            "text/plain": [
              "<Figure size 1440x360 with 2 Axes>"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.7/dist-packages/seaborn/_decorators.py:43: FutureWarning: Pass the following variable as a keyword arg: x. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.\n",
            "  FutureWarning\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 1440x360 with 2 Axes>"
            ]
          },
          "metadata": {}
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "dfx = dft.reset_index()[['cap-shape', 'odor', 'bruises', 'gill-color', 'spore-print-color', 'population']] # ทำการแยก feature ออกจาก dataframe หลัก\n",
        "dfx = dfx.astype('category') # แปลง type ให้เป็น category ให้ง่ายต่อการนำไปแยกทำ dummy_clf\n",
        "dfx.info() # แสดง type ออกมาดูจะเห็นได้ว่าเปลี่ยนเป็น category แล้ว\n",
        "\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "h-inh5vqXpzd",
        "outputId": "5b6da6f7-e2e6-4e31-f6ef-8664b3c9768c"
      },
      "execution_count": 11,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "<class 'pandas.core.frame.DataFrame'>\n",
            "RangeIndex: 8124 entries, 0 to 8123\n",
            "Data columns (total 6 columns):\n",
            " #   Column             Non-Null Count  Dtype   \n",
            "---  ------             --------------  -----   \n",
            " 0   cap-shape          8124 non-null   category\n",
            " 1   odor               8124 non-null   category\n",
            " 2   bruises            8124 non-null   category\n",
            " 3   gill-color         8124 non-null   category\n",
            " 4   spore-print-color  8124 non-null   category\n",
            " 5   population         8124 non-null   category\n",
            "dtypes: category(6)\n",
            "memory usage: 49.4 KB\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "dfx = dfx.merge(_class, left_index=True, right_index=True) # ทำการ merge class หรือ target เข้ารวม dataframe ที่จะนำไปทำ dummy_clf\n",
        "display(dfx.info())\n",
        "dfx.head() # แสดง type ออกมาดูจะเห็นได้ว่ามีคลาสที่ยังเป็น object เพื่อแยกว่าอันไหน feature อันนไหน class\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 482
        },
        "id": "ZpcFqQ7BXsIT",
        "outputId": "a2c2e51c-4bcf-43f7-bed3-2a123a58feb3"
      },
      "execution_count": 12,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "<class 'pandas.core.frame.DataFrame'>\n",
            "RangeIndex: 8124 entries, 0 to 8123\n",
            "Data columns (total 7 columns):\n",
            " #   Column             Non-Null Count  Dtype   \n",
            "---  ------             --------------  -----   \n",
            " 0   cap-shape          8124 non-null   category\n",
            " 1   odor               8124 non-null   category\n",
            " 2   bruises            8124 non-null   category\n",
            " 3   gill-color         8124 non-null   category\n",
            " 4   spore-print-color  8124 non-null   category\n",
            " 5   population         8124 non-null   category\n",
            " 6   class              8124 non-null   object  \n",
            "dtypes: category(6), object(1)\n",
            "memory usage: 112.9+ KB\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "text/plain": [
              "None"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>cap-shape</th>\n",
              "      <th>odor</th>\n",
              "      <th>bruises</th>\n",
              "      <th>gill-color</th>\n",
              "      <th>spore-print-color</th>\n",
              "      <th>population</th>\n",
              "      <th>class</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>x</td>\n",
              "      <td>p</td>\n",
              "      <td>t</td>\n",
              "      <td>k</td>\n",
              "      <td>k</td>\n",
              "      <td>s</td>\n",
              "      <td>p</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>x</td>\n",
              "      <td>a</td>\n",
              "      <td>t</td>\n",
              "      <td>k</td>\n",
              "      <td>n</td>\n",
              "      <td>n</td>\n",
              "      <td>e</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>b</td>\n",
              "      <td>l</td>\n",
              "      <td>t</td>\n",
              "      <td>n</td>\n",
              "      <td>n</td>\n",
              "      <td>n</td>\n",
              "      <td>e</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>x</td>\n",
              "      <td>p</td>\n",
              "      <td>t</td>\n",
              "      <td>n</td>\n",
              "      <td>k</td>\n",
              "      <td>s</td>\n",
              "      <td>p</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>x</td>\n",
              "      <td>n</td>\n",
              "      <td>f</td>\n",
              "      <td>k</td>\n",
              "      <td>n</td>\n",
              "      <td>a</td>\n",
              "      <td>e</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "</div>"
            ],
            "text/plain": [
              "  cap-shape odor bruises gill-color spore-print-color population class\n",
              "0         x    p       t          k                 k          s     p\n",
              "1         x    a       t          k                 n          n     e\n",
              "2         b    l       t          n                 n          n     e\n",
              "3         x    p       t          n                 k          s     p\n",
              "4         x    n       f          k                 n          a     e"
            ]
          },
          "metadata": {},
          "execution_count": 12
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "cat_df = dfx.select_dtypes(include=['category']).columns.to_list() # ทำการเข้าขั้นตอน dummy_clf เราจะนำ type category แยกออกมาทำ dummy_clf เพื่อแยก feature ออกจากกัน\n",
        "one_hot_df = pd.get_dummies(dfx, columns=cat_df) # ซึ่งเราจะสังเกตได้ว่าในหนึ่ง feature แตกตัวออกจากกัน\n",
        "one_hot_df\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 479
        },
        "id": "A5eZB6BHXxsm",
        "outputId": "cf0a0bb7-d872-4b41-cd49-4c79113c8806"
      },
      "execution_count": 13,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>class</th>\n",
              "      <th>cap-shape_b</th>\n",
              "      <th>cap-shape_c</th>\n",
              "      <th>cap-shape_f</th>\n",
              "      <th>cap-shape_k</th>\n",
              "      <th>cap-shape_s</th>\n",
              "      <th>cap-shape_x</th>\n",
              "      <th>odor_a</th>\n",
              "      <th>odor_c</th>\n",
              "      <th>odor_f</th>\n",
              "      <th>odor_l</th>\n",
              "      <th>odor_m</th>\n",
              "      <th>odor_n</th>\n",
              "      <th>odor_p</th>\n",
              "      <th>odor_s</th>\n",
              "      <th>odor_y</th>\n",
              "      <th>bruises_f</th>\n",
              "      <th>bruises_t</th>\n",
              "      <th>gill-color_b</th>\n",
              "      <th>gill-color_e</th>\n",
              "      <th>gill-color_g</th>\n",
              "      <th>gill-color_h</th>\n",
              "      <th>gill-color_k</th>\n",
              "      <th>gill-color_n</th>\n",
              "      <th>gill-color_o</th>\n",
              "      <th>gill-color_p</th>\n",
              "      <th>gill-color_r</th>\n",
              "      <th>gill-color_u</th>\n",
              "      <th>gill-color_w</th>\n",
              "      <th>gill-color_y</th>\n",
              "      <th>spore-print-color_b</th>\n",
              "      <th>spore-print-color_h</th>\n",
              "      <th>spore-print-color_k</th>\n",
              "      <th>spore-print-color_n</th>\n",
              "      <th>spore-print-color_o</th>\n",
              "      <th>spore-print-color_r</th>\n",
              "      <th>spore-print-color_u</th>\n",
              "      <th>spore-print-color_w</th>\n",
              "      <th>spore-print-color_y</th>\n",
              "      <th>population_a</th>\n",
              "      <th>population_c</th>\n",
              "      <th>population_n</th>\n",
              "      <th>population_s</th>\n",
              "      <th>population_v</th>\n",
              "      <th>population_y</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>p</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>e</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>e</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>p</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>e</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8119</th>\n",
              "      <td>e</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8120</th>\n",
              "      <td>e</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8121</th>\n",
              "      <td>e</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8122</th>\n",
              "      <td>p</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8123</th>\n",
              "      <td>e</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>8124 rows × 45 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "     class  cap-shape_b  cap-shape_c  ...  population_s  population_v  population_y\n",
              "0        p            0            0  ...             1             0             0\n",
              "1        e            0            0  ...             0             0             0\n",
              "2        e            1            0  ...             0             0             0\n",
              "3        p            0            0  ...             1             0             0\n",
              "4        e            0            0  ...             0             0             0\n",
              "...    ...          ...          ...  ...           ...           ...           ...\n",
              "8119     e            0            0  ...             0             0             0\n",
              "8120     e            0            0  ...             0             1             0\n",
              "8121     e            0            0  ...             0             0             0\n",
              "8122     p            0            0  ...             0             1             0\n",
              "8123     e            0            0  ...             0             0             0\n",
              "\n",
              "[8124 rows x 45 columns]"
            ]
          },
          "metadata": {},
          "execution_count": 13
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "\n",
        "class MultiColumnLabelEncoder: # ทำการสร้างคลาส ในการทำ preprocessing data\n",
        "\n",
        "    def __init__(self, columns=None):\n",
        "        \"\"\"\n",
        "\n",
        "        :param columns:\n",
        "        \"\"\"\n",
        "        self.columns = columns # array of column names to encode\n",
        "\n",
        "\n",
        "    def fit(self, X, y=None):\n",
        "        \"\"\"\n",
        "\n",
        "        :param X:\n",
        "        :param y:\n",
        "        :return:\n",
        "        \"\"\"\n",
        "        self.encoders = {}\n",
        "        columns = X.columns if self.columns is None else self.columns\n",
        "        for col in columns:\n",
        "            self.encoders[col] = LabelEncoder().fit(X[col])\n",
        "        return self\n",
        "\n",
        "\n",
        "    def transform(self, X):\n",
        "        \"\"\"\n",
        "\n",
        "        :param X:\n",
        "        :return:\n",
        "        \"\"\"\n",
        "        output = X.copy()\n",
        "        columns = X.columns if self.columns is None else self.columns\n",
        "        for col in columns:\n",
        "          output[col] = self.encoders[col].transform(X[col])\n",
        "        return output\n",
        "\n",
        "\n",
        "    def fit_transform(self, X, y=None):\n",
        "        \"\"\"\n",
        "\n",
        "        :param X:\n",
        "        :param y:\n",
        "        :return:\n",
        "        \"\"\"\n",
        "        return self.fit(X,y).transform(X)\n",
        "\n",
        "\n",
        "    def inverse_transform(self, X):\n",
        "        \"\"\"\n",
        "\n",
        "        :param X:\n",
        "        :return:\n",
        "        \"\"\"\n",
        "        output = X.copy()\n",
        "        columns = X.columns if self.columns is None else self.columns\n",
        "        for col in columns:\n",
        "            output[col] = self.encoders[col].inverse_transform(X[col])\n",
        "        return output\n"
      ],
      "metadata": {
        "id": "gZz88XMqXy5W"
      },
      "execution_count": 14,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "multi = MultiColumnLabelEncoder(columns=['class']) # บอกตำแหน่งที่จะ preprocess\n",
        "df = multi.fit_transform(one_hot_df) # ทำการ preprocess label encoder จาก text เป็นตัวเลข\n",
        "display(set(df['class'])) # จะเห็นได้ว่า p, e แปลงเป็น 0,1 แล้ว\n",
        "df # มาดูสรุปตารางจะเห็นได้ว่าข้อมูลพร้อมนำเข้า model แล้ว\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 497
        },
        "id": "XAxPUz3jX0Wl",
        "outputId": "9e99aaee-eec5-492b-a999-4874827d14cf"
      },
      "execution_count": 15,
      "outputs": [
        {
          "output_type": "display_data",
          "data": {
            "text/plain": [
              "{0, 1}"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>class</th>\n",
              "      <th>cap-shape_b</th>\n",
              "      <th>cap-shape_c</th>\n",
              "      <th>cap-shape_f</th>\n",
              "      <th>cap-shape_k</th>\n",
              "      <th>cap-shape_s</th>\n",
              "      <th>cap-shape_x</th>\n",
              "      <th>odor_a</th>\n",
              "      <th>odor_c</th>\n",
              "      <th>odor_f</th>\n",
              "      <th>odor_l</th>\n",
              "      <th>odor_m</th>\n",
              "      <th>odor_n</th>\n",
              "      <th>odor_p</th>\n",
              "      <th>odor_s</th>\n",
              "      <th>odor_y</th>\n",
              "      <th>bruises_f</th>\n",
              "      <th>bruises_t</th>\n",
              "      <th>gill-color_b</th>\n",
              "      <th>gill-color_e</th>\n",
              "      <th>gill-color_g</th>\n",
              "      <th>gill-color_h</th>\n",
              "      <th>gill-color_k</th>\n",
              "      <th>gill-color_n</th>\n",
              "      <th>gill-color_o</th>\n",
              "      <th>gill-color_p</th>\n",
              "      <th>gill-color_r</th>\n",
              "      <th>gill-color_u</th>\n",
              "      <th>gill-color_w</th>\n",
              "      <th>gill-color_y</th>\n",
              "      <th>spore-print-color_b</th>\n",
              "      <th>spore-print-color_h</th>\n",
              "      <th>spore-print-color_k</th>\n",
              "      <th>spore-print-color_n</th>\n",
              "      <th>spore-print-color_o</th>\n",
              "      <th>spore-print-color_r</th>\n",
              "      <th>spore-print-color_u</th>\n",
              "      <th>spore-print-color_w</th>\n",
              "      <th>spore-print-color_y</th>\n",
              "      <th>population_a</th>\n",
              "      <th>population_c</th>\n",
              "      <th>population_n</th>\n",
              "      <th>population_s</th>\n",
              "      <th>population_v</th>\n",
              "      <th>population_y</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8119</th>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8120</th>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8121</th>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8122</th>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8123</th>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>8124 rows × 45 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "      class  cap-shape_b  cap-shape_c  ...  population_s  population_v  population_y\n",
              "0         1            0            0  ...             1             0             0\n",
              "1         0            0            0  ...             0             0             0\n",
              "2         0            1            0  ...             0             0             0\n",
              "3         1            0            0  ...             1             0             0\n",
              "4         0            0            0  ...             0             0             0\n",
              "...     ...          ...          ...  ...           ...           ...           ...\n",
              "8119      0            0            0  ...             0             0             0\n",
              "8120      0            0            0  ...             0             1             0\n",
              "8121      0            0            0  ...             0             0             0\n",
              "8122      1            0            0  ...             0             1             0\n",
              "8123      0            0            0  ...             0             0             0\n",
              "\n",
              "[8124 rows x 45 columns]"
            ]
          },
          "metadata": {},
          "execution_count": 15
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "y = df['class'] # แยก class ออกนำไปใส่ตัวแปล y\n",
        "X = df.drop(['class'],axis=1) # แยก feature ออกนำไปใส่ X\n"
      ],
      "metadata": {
        "id": "9eJtsnoCX2Pg"
      },
      "execution_count": 16,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.20, random_state=5) # ทำการ split data ออกจากกัน เพื่อแยกว่าอันไหนเทรนอันไหนเทส\n"
      ],
      "metadata": {
        "id": "zxgHdiXHX3mD"
      },
      "execution_count": 17,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "logreg = LogisticRegression() # เราจะใช้ model ที่มีชื่อว่า LogisticRegression\n",
        "logreg.fit(X_train,y_train) # ทำการ train data\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "foJG9rDjX4wf",
        "outputId": "5a8c9811-4b14-4e85-cd58-d4bfaa2680c5"
      },
      "execution_count": 18,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "LogisticRegression()"
            ]
          },
          "metadata": {},
          "execution_count": 18
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "prediction = logreg.predict(X_test) # เก็บค่า prediction ไว้ในตัวแปล prediction\n",
        "acc = metrics.accuracy_score(y_test, prediction) # สร้างตัวแปลเพื่อดูค่า accuracy score\n",
        "report = metrics.classification_report(y_test, prediction) # สร้าง report สรุปการ prediction ออกมาดู\n",
        "print(report)\n",
        "acc # ซึ่งเราจะเห็นได้ว่า model เรามีความถูกต้องถึง 99.3 % เลยทีเดียว ถือว่าใช้งาน"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "NguM5ZyRYqCL",
        "outputId": "289591dc-de7e-4981-d444-b55c7f24960d"
      },
      "execution_count": 19,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "              precision    recall  f1-score   support\n",
            "\n",
            "           0       0.99      1.00      0.99       827\n",
            "           1       1.00      0.99      0.99       798\n",
            "\n",
            "    accuracy                           0.99      1625\n",
            "   macro avg       0.99      0.99      0.99      1625\n",
            "weighted avg       0.99      0.99      0.99      1625\n",
            "\n"
          ]
        },
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "0.9938461538461538"
            ]
          },
          "metadata": {},
          "execution_count": 19
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "# กรณีต้องการ turning machine หรือ turn model เพิ่ม ให้ทำตามขั้นตอนนี้เพื่อหาค่า hyper parameter\n",
        "scaler = StandardScaler()\n",
        "X = scaler.fit_transform(df.values[:, 1:])\n",
        "X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.20, random_state=5) # ทำการ split data ออกจากกัน เพื่อแยกว่าอันไหนเทรนอันไหนเทส\n",
        "penalty = ['l2']\n",
        "c_range = [0.001, 0.01, 0.1, 1]\n",
        "solver = ['lbfgs']\n",
        "parameters_lr = dict(C=c_range, penalty=penalty, solver=solver)\n",
        "lr_1 = GridSearchCV(logreg,parameters_lr,scoring='accuracy', cv=3,verbose=1)\n",
        "\n",
        "\n",
        "\n"
      ],
      "metadata": {
        "id": "6Ir6ZWjdX8YC"
      },
      "execution_count": 20,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "lr_1.fit(X_train,y_train)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "EDu2oWnnX9jD",
        "outputId": "90cfbe30-e0c2-4fd6-948c-6bb31bb90fda"
      },
      "execution_count": 21,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Fitting 3 folds for each of 4 candidates, totalling 12 fits\n"
          ]
        },
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "GridSearchCV(cv=3, estimator=LogisticRegression(),\n",
              "             param_grid={'C': [0.001, 0.01, 0.1, 1], 'penalty': ['l2'],\n",
              "                         'solver': ['lbfgs']},\n",
              "             scoring='accuracy', verbose=1)"
            ]
          },
          "metadata": {},
          "execution_count": 21
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "def best_model(model_name):\n",
        "    print(model_name.best_score_)\n",
        "    print(model_name.best_params_)\n",
        "    print(model_name.best_estimator_)\n",
        "\n",
        "best_model(lr_1)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "D8oinPIJYDGc",
        "outputId": "c76bec58-431f-487c-ee7d-362e4d203b6f"
      },
      "execution_count": 22,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "0.9959994790204164\n",
            "{'C': 1, 'penalty': 'l2', 'solver': 'lbfgs'}\n",
            "LogisticRegression(C=1)\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "\n",
        "y_pred_lr_1 = lr_1.best_estimator_.predict(X_test)\n",
        "acc = metrics.accuracy_score(y_test, y_pred_lr_1)\n",
        "report = metrics.classification_report(y_test, y_pred_lr_1)\n",
        "display(lr_1.best_estimator_) # print ดูค่า hyper parameters ที่เราได้จากการ turning machine\n",
        "display(y_pred_lr_1)\n",
        "print(report) # จะเห็นได้ว่าระหว่าง turn model กับไม่ได้มีการ turn model จะมีค่า accuracy ที่เพิ่มขึ้นมา ซึ่งช่วยให้ model เรามีความแม่นยำมาขึ้นนั้นเอง\n",
        "acc # 99.5 % ดีกว่าเดิม\n",
        "# จริงๆสามารถทำ A/B Testing model ตัวอื่นได้นะพวก KNN, Decision Tree เป็นต้น ค่าจะไม่ต่างกันมากเท่าไหร่"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 237
        },
        "id": "7A8s1YP2YEGI",
        "outputId": "fe9f037a-98e1-4563-f735-29605a7b3555"
      },
      "execution_count": 23,
      "outputs": [
        {
          "output_type": "display_data",
          "data": {
            "text/plain": [
              "LogisticRegression(C=1)"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "display_data",
          "data": {
            "text/plain": [
              "array([1, 1, 1, ..., 1, 1, 1])"
            ]
          },
          "metadata": {}
        },
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "              precision    recall  f1-score   support\n",
            "\n",
            "           0       0.99      1.00      1.00       827\n",
            "           1       1.00      0.99      0.99       798\n",
            "\n",
            "    accuracy                           1.00      1625\n",
            "   macro avg       1.00      0.99      1.00      1625\n",
            "weighted avg       1.00      1.00      1.00      1625\n",
            "\n"
          ]
        },
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "0.9950769230769231"
            ]
          },
          "metadata": {},
          "execution_count": 23
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        ""
      ],
      "metadata": {
        "id": "YOhYzhVUYJL9"
      },
      "execution_count": 23,
      "outputs": []
    }
  ]
}

```
