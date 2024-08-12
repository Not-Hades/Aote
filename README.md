import org.bukkit.event.EventHandler;
import org.bukkit.command.Command;
import org.bukkit.Bukkit;
import org.bukkit.Material;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.bukkit.event.Listener;
import org.bukkit.event.player.PlayerInteractEvent;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.ItemMeta;
import org.bukkit.plugin.java.JavaPlugin;
import org.bukkit.scheduler.BukkitRunnable;
import java.util.Arrays;

public class SkyblockPlugin extends JavaPlugin implements Listener {
    @Override
    public void onEnable() {
        getServer().getPluginManager().registerEvents(this, this);
        getCommand("ciaote").setExecutor(this); 
        setupRecipe();
    }

    public ItemStack createAspectOfTheEnd() {
        ItemStack item = new ItemStack(Material.DIAMOND_SWORD);
        ItemMeta meta = item.getItemMeta();
        if (meta != null) {
            meta.setDisplayName("&9Aspect of the End");
            meta.setLore(Arrays.asList("&7Damage:&c+100", "&7Strength:&c+100", " ", "&6Ability: &e&lRIGHT CLICK", "Teleports you to 8 block forward", "&bMana Cost:&850", " ", "&7Cooldown:&73 seconds", " ", "&8This item can be.reforged", "&9&lRARE SWORD"));
            item.setItemMeta(meta);
        }
        return item;
    }

    public void setupRecipe() {
//recipe 
        org.bukkit.inventory.Recipe aote = new org.bukkit.inventory.ShapedRecipe(createAspectOfTheEnd())
                .shape(" B ", " B ", " T ")
                .setIngredient('B', Material.ENDER_PEARL)
                .setIngredient('T', Material.DIAMOND);
        Bukkit.addRecipe(aote);
    }

    @Override
    public boolean onCommand(CommandSender sender, Command command, String label, String[] args) {
        if (command.getName().equalsIgnoreCase("ciaote")) {
            if (sender instanceof Player) {
                Player player = (Player) sender;
                if (player.isOp()) { 
                    player.getInventory().addItem(createAspectOfTheEnd());
                    player.sendMessage("&6&l[+]Aspect Of End");
                    return true;
                } else {
                    player.sendMessage("&cNo permission.");
                    return true;
                }
            } else {
                sender.sendMessage("&7Operators can only use this!");
                return true;
            }
        }
        return false;
    }

    @EventHandler
    public void onPlayerInteract(PlayerInteractEvent event) {
        if (event.getItem() != null && event.getItem().isSimilar(createAspectOfTheEnd())) {
            Player player = event.getPlayer();

            
            if (player.getCooldown(Material.DIAMOND_SWORD) > 0) {
                player.sendMessage("&7Cooldown!!");
                return;
            }


            teleportForward(player);

            // cool down 
            applyCooldown(player);
        }
    }

    private void teleportForward(Player player) {
      
        org.bukkit.util.Vector 


            
direction = player.getLocation().getDirection().normalize().multiply(8);
        org.bukkit.Location newLocation = player.getLocation().add(direction);
        player.teleport(newLocation);
        
    }

    private void applyCooldown(Player player) {
        
        final long cooldownTime = 250L; 

        player.setCooldown(Material.DIAMOND_SWORD, (int) cooldownTime);

        new BukkitRunnable() {
            @Override
            public void run() {
                player.sendMessage("&7Cool-Down finished &6&lRight Click &7to use ");
            }
        }.runTaskLater(this, cooldownTime);
    }
}