# java763827359
test
/**  
* 
* @ClassName: heart
* @Description: TODO
* @author 王宣淇
* @date 2018年10月7日 下午9:30:01
*
*/
import java.util.ArrayList;

import java.util.List;

import java.util.Scanner;
public class Main {
	/**
	 * 使用List管理玩家的角色，在添加随从和随从死亡后从场上移除，将会起到非常方便的作用。
	 */
	public static List<role> p1 = new ArrayList<>();

	public static List<role> p2 = new ArrayList<>();

	/**
	 * 另外定义两个指向p1,p2 的引用，便于交换回合后 p1仍然表示先手玩家，p2为后手玩家
	 */

	public static List<role> firstAttack;

	public static List<role> lastAttack;

	public static void main(String[] args) {

		Scanner scanner = new Scanner(System.in);

		firstAttack = p1;

		lastAttack = p2;

		role hero1 = new role(0, 30);

		role hero2 = new role(0, 30);

		/**
		 * 将英雄添加到0位置
		 */

		p1.add(hero1);

		p2.add(hero2);

		int n = Integer.parseInt(scanner.nextLine());

		String[] cmd;

		String temp;

		for (int i = 0; i < n; i++) {

			temp = scanner.nextLine() + " ";//在每条指令末尾加上一个空格，便于下面的split()方法分割字符串到cmd数组中

			cmd = temp.split(" ");//每条指令按空格分割成几部分

			if (cmd.length == 4) {//表示召唤随从的指令(summon、位置、攻击力、生命值，加起来长度为4)

				action(cmd[0], Integer.parseInt(cmd[1]), Integer.parseInt(cmd[2]), Integer.parseInt(cmd[3]));

			} else if (cmd.length == 3) {//攻击的指令(attack、己方位置、对方位置，加起来长度为3)

				action(cmd[0], Integer.parseInt(cmd[1]), Integer.parseInt(cmd[2]), 0);

			} else {//输入的是end指令  交换回合

				action(cmd[0], 0, 0, 0);

			}

		}

		/**
		 * 判断先手获胜还是后手
		 */

		if (p1.get(0).health <= 0)

			System.out.println(-1);

		else {

			if (p2.get(0).health > 0)

				System.out.println(0);

			else

				System.out.println(1);

		}

		//输出双方结果

		result(p1);

		result(p2);

		scanner.close();

	}

	/**
	 * action（）方法是对每条指令作为相应操作
	 * 当为召唤随从指令时:operate:summon,a:在什么位置召唤随从,b:攻击力，c:生命值
	 * 当为攻击指令时:operate:attack，a:己方位置，b:对方位置，c:传入值为0，只是为了区别其它指令
	 * 当为end指令:operate:end   其它a、b、c传入值都为0，只用来区别其它指令
	 */

	public static void action(String operate, int a, int b, int c) {

		if (operate.equals("summon"))

			firstAttack.add(a, new role(b, c));//召唤一个随从

		else if (operate.equals("attack")) {

			//两方攻击后生命值减少

			firstAttack.get(a).health -= lastAttack.get(b).attack;

			lastAttack.get(b).health -= firstAttack.get(a).attack;

			dieOfRetinue();//每次攻击完后都必须判断双方中是否有随从死亡

		} else if(operate.equals("equip")) {
			int t = firstAttack.get(a).health;
			firstAttack.add(0,new role(a,t));
		}else {
			//通过firstAttack、lastAttack交换双方，从而p1一直是先手，p2一直是后手，不会因为交换而改变

			List<role> t;

			t = firstAttack;

			firstAttack = lastAttack;

			lastAttack = t;

		}

 

	}

	/*

	 * 判断是否有随从死亡，有随从死亡，将移除该位置的随从

	 */

	public static void dieOfRetinue() {

		for (int i = 1; i < p1.size(); i++) {

			if (p1.get(i).health <= 0)//找到生命值小于等于0的随从的位置

				p1.remove(i);//移除死亡随从

		}

		for (int j = 1; j < p2.size(); j++) {

			if (p2.get(j).health <= 0)

				p2.remove(j);

		}

	}

	/*

	 * 输出最后双方角色的结果

	 */

	public static void result(List<role> L) {

		System.out.println(L.get(0).health);//输出英雄生命值

		System.out.print(L.size() - 1 + " ");//输出剩余随从

		if (L.size() > 1) {//有随从的时候。输出每个随从生命值

			for (int i = 1; i < L.size(); i++)

				System.out.print(L.get(i).health + " ");

			System.out.println();

		} else {//随从都死亡

			System.out.println();

		}

	}

 

}

/**

 * 定义角色，将角色的生命值和攻击力封装成role

 */

class role {

	public int health;

	public int attack;

	public role(int a, int h) {

		health = h;

		attack = a;

	}

}
