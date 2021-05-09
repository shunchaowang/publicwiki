## Projection
Projection can be used on Spring Data Repository interface to customze the query result only including the attributes
needed instead of the whole domain with all cascaded associations. It provides the encapsulation for business logic, and
ploymorphism for query mechanism. There are 2 major projection types, interface based and class based. Interface based
projections support nested projections, but class based don't. If the query needs the result with association, interface
based projections are the choice. It's usually called DTO - Data Transfer Object.
### Closed Interface Projection
The is the the most customizable and efficient projection to be used with spring data repository. 
This is the preferred projection. The default method can be added for extra attributes with java 8+. Using the example of RBAC, here is the projection for user with role. 
```
import java.util.Set;
import static java.util.stream.Collectors.joining;

public interface UserInfo {
    String getUsername();
    Set<RoleInfo> getRoles(); // name must match entity attributes if not specified in the jpql
    // create another project for nested attributes
    // projections are used in the jpql format, so the assocations must be customized with native types.
    interface RoleInfo {
        String getName(); // exact matching the name of role attribute
    }
    
    // default method can be added to add more projection fields, this methods with concatenate all roles for display
    // purpose
    default String getRoleName() {
        // must call existing methods to do the transformation
        return getRoles().stream().map(RoleInfo::getName).collect(joining("|"));
    }
}
```
### Open Interface Projection
Open interface can have a #target to pull the attribute from associations.
### Class Projection
The projection is represented by a DTO class with constructor of all native data types.
## Query annotation
Projection dto can be used with Query annotation in jpql format to provide more flexible queries on spring repository.
Here are queries using UserInfo in UserRepository.
```
@Repository
public interface UserRepository extends PagingAndSortingRepository<User, Long> {
    
    // the new query methods follow overloading/overriding ploicies.
    // fetch user by username and return a UserInfo projection with positioned parameters
    // the as <name> in the select must exactly match projection attribute name
    @Query("select u.username as username, r as roles from User u join u.roles r where u.username = ?1")
    UserInfo fetchByPositionedUsername(String username);
    @Query("select u.username as username, r as roles from User u join u.roles r where u.username = :name")
    UserInfo fetchByNamedUsername(@Param("name")String username);
    
    String findUserWithRoleQuery = "select u.username as username, r as roles from User u join u.roles r";
    String findUserCountQuery = "select count(u) from User u";
    
    // since this is a projection query, countQuery must be provided for the totalElements in the Page info
    @Query(value = findUserWithRoleQuery, countQuery = findUserCountQuery)
    Page<UserInfo> fetchAll(Pageable pageable);
}
```
## Example
Here is a very common RBAC model, User and Role with many-to-many association, Client and Role with a one-to-many
association. The model is like below.
```
import javax.persistence.*;

@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;
    @Column(nullable = false, unique = true)
    private String username;
    @Column(nullable = false)
    private String password;
    @Column(nullable = false)
    private boolean active;
    @Version
    private long version;
    @ManyToMany(fetch = FetchType.LAZY) // lazy is the default fetch strategy, we want to keep it to avoid n+1 query.
    @JoinTable(name = "users_roles", joinColumns = @JoinColumn(name = "user_id", nullable = false),
                inverseJoinColumns = @JoinColumn(name = "role_id", nullable = false))
    private Set<Role> roles;
    
    // omits getters and setters
}

@Entity
@Table(name = "roles")
public class Role {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;
    @Column(nullable = false, unique = true)
    private String name;
    @Version
    private long version;
    @ManyToMany(mappedBy = "roles") // the attribute of User entity
    private Set<User> users;
    @ManyToOne
    @JoinColumn(name = "client_id", nullable = false)
    private Client client;
    
    // omits getters and setters
}

@Entity
@Table(name = "clients")
public class Client {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;
    @Column(nullable = false, unique = true)
    private String name;
    @Version
    private long version;
    @OneToMany(mappedBy = "client")
    private Set<Role> roles;
    
    // omits getters and setters
}
